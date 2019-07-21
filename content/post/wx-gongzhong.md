---
title: 'nodejs微信jssdk后端接口'
date: 2016-11-25 10:29:25
categories: ['node']
tags: ['node', 'wechat']
---

> 写过了两个微信的页面，遇到了挺多不会的问题，当时也是自己边查资料，边实践完成了简单的需求，刚好现在有空，把之前的东西整理一遍。

与普通的手机页面不同的是，微信页面提供给你了调用微信APP内置功能的接口，可以实现更复杂的功能。
## jssdk的前端使用
- 前端页面调用jssdk首先要通绑定“公众号设置”的“功能设置”里填写“JS接口安全域名”
- 然后在页面中引入http://res.wx.qq.com/open/js/jweixin-1.2.0.js 
- 调用 wx.config({...}) 来验证权限配置
- 然后可根据需要 调用[微信所提供的接口 ](https://mp.weixin.qq.com/wiki)
## 后端返回接口
在前端调用时wx.config({...})中需要的参数需要我们自己进行返回
```
wx.config({
    debug: true, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
    appId: '', // 必填，公众号的唯一标识
    timestamp: , // 必填，生成签名的时间戳
    nonceStr: '', // 必填，生成签名的随机串
    signature: '',// 必填，签名
    jsApiList: [] // 必填，需要使用的JS接口列表
});
```

其中 `timestamp` , `nonceStr`, `signature`,是需要后端计算返回的。
### 签名获取方法

>签名生成规则如下：参与签名的字段包括noncestr（随机字符串）, 有效的jsapi_ticket, timestamp（时间戳）, url（当前网页的URL，不包含#及其后面部分） 。对所有待签名参数按照字段名的ASCII 码从小到大排序（字典序）后，使用URL键值对的格式（即key1=value1&key2=value2…）拼接成字符串string1。这里需要注意的是所有参数名均为小写字符。对string1作sha1加密，字段名和字段值都采用原始值，不进行URL 转义。

而其中的 jsapi_ticket 是通过 access_token 来获取的，且两者都有过期时间（7200秒）其中 jsapi_ticket 更是限制了获取次数。
所以为了保存两者，使用redis数据库保存在内存中是个很好的选择（可快速读取，并设置过期时间）。

### token获取方法：
```
/**
 * 获取token
 * @return {promise} res 值为token
 */
function getToken () {
  return redis.getVal('token')  // 首先读取 redis 是否存在token
    .then(function (res) {  
      if (res === null) {   // 若不存在，则返回savetoken() 获取
        // console.log('不存在token 调用saveToken')
        return saveToken ()
      } else {              // 若存在 则直接返回
        // console.log('存在token 直接返回')
        return res 
      }
    })
    .catch(function (err) {   // 捕获 错误
      console.log(err)
    })
}

/**
 * 从服务端获取token 并保存在redis中
 * @return {promise} 值 为 token
 */
function saveToken () {

  return new Promise((resolve, reject) => {

    let reqUrl = config.gettoken_url // https://api.weixin.qq.com/cgi-bin/token?
    let params = {
      grant_type: 'client_credential',
      appid: config.appid,
      secret: config.appsecret
    }

    let options = {
      method: 'get',
      url: reqUrl + qs.stringify(params)
    }

    request(options, function (err, res, body) {
      if (res) {
        let bodys = JSON.parse(body)
        let expires = bodys.expires_in
        let token = bodys.access_token
        
        redis.setKey('token', token, expires)  // 将token 保存到 redis
          .catch(function (err) {
            console.log(err)
          })

        resolve(token)
      } else {
        reject(err)
      }
    })
  })
}

```
在配置文件中配置好所需要的appid和appsecret，首先查看redis中是否存在，如果存在就直接返回，没有的话，就调用saveToken去获取并保存在redis中
### jsapi_ticket 获取方法
同理，jsapi_ticket 也采用同样的方式去获取
```
/**
 * 获取ticket
 * @return {promise} res 值为ticket
 */

function getJsTicket() {  // 获取token
  return redis.getVal('ticket')  // 首先读取 redis 是否存在ticket
    .then(function (res) {  
      if (res === null) {   // 若不存在，则返回saveJsTicket() 获取
        // console.log('不存在ticket 调用saveJsTicket')
        return saveJsTicket ()
      } else {              // 若存在 则直接返回
        // console.log('存在ticket 直接返回')
        return res 
      }
    })
    .catch(function (err) {   // 捕获 错误
      console.log(err)
    })
}

/**
 * 从服务端获取ticket 并保存在redis中
 * @return {promise} 值 为 ticket
 */
function saveJsTicket () {

  return new Promise((resolve, reject) => {

    getToken().then(function (token) {

      let reqUrl = config.ticket_start + token + config.ticket_end
      let options = {
        method: 'get',
        url: reqUrl
      }

      request(options, function (err, res, body) {
        if (res) {
          let bodys = JSON.parse(body)     // 解析微信服务器返回的
          let ticket = bodys.ticket        // 获取 ticket
          let expires = bodys.expires_in   // 获取过期时间

          redis.setKey('ticket', ticket, expires)  // 将ticket 保存到 redis
            .catch(function (err) {
              console.log(err)
            })
          resolve(ticket)
        } else {
          reject(err)
        }
      })
    }).catch(function (err) {
        console.log(err)
    })
  })
}
```
### 签名算法
在获取jsapi_ticket后就可以生成JS-SDK权限验证的签名了
```
/**
 * 1. appId 必填，公众号的唯一标识
 * 2. timestamp 必填，生成签名的时间戳
 * 3. nonceStr 必填，生成签名的随机串
 * 4. signature 必填，签名
 */

const crypto = require('crypto')
const getJsTicket = require('./getJsTicket')
const config = require('../../config')  // 微信设置

// sha1加密
function sha1(str) {
  let shasum = crypto.createHash("sha1")
  shasum.update(str)
  str = shasum.digest("hex")
  return str
}

/**
 * 生成签名的时间戳
 * @return {字符串} 
 */
function createTimestamp () {
  return parseInt(new Date().getTime() / 1000) + ''
}

/**
 * 生成签名的随机串
 * @return {字符串} 
 */
function createNonceStr () {
  return Math.random().toString(36).substr(2, 15)
}

/**
 * 对参数对象进行字典排序
 * @param  {对象} args 签名所需参数对象
 * @return {字符串}    排序后生成字符串
 */
function raw (args) {
  var keys = Object.keys(args)
  keys = keys.sort()
  var newArgs = {}
  keys.forEach(function (key) {
    newArgs[key.toLowerCase()] = args[key]
  })

  var string = ''
  for (var k in newArgs) {
    string += '&' + k + '=' + newArgs[k]
  }
  string = string.substr(1)
  return string
}

/**
* @synopsis 签名算法 
*
* @param jsapi_ticket 用于签名的 jsapi_ticket
* @param url 用于签名的 url ，注意必须动态获取，不能 hardcode
*
* @returns {对象} 返回微信jssdk所需参数对象
*/
function sign (jsapi_ticket, url) {
  var ret = {
    jsapi_ticket: jsapi_ticket,
    nonceStr: createNonceStr(),
    timestamp: createTimestamp(),
    url: url
  }
  var string = raw(ret)
  ret.signature = sha1(string)
  ret.appId =  config.appid
  return ret
}

/**
 * 返回微信jssdk 所需参数对象
 * @param  {字符串} url 当前访问URL
 * @return {promise}     返回promise类 val为对象
 */
function jsSdk (url) {
  return getJsTicket()
    .then(function (ticket) {
      return sign(ticket, url)
    })
    .catch(function (err) {
      console.log(err)
    })
}

function routerSdk (req, res, next) {
  let clientUrl = req.body.url
  if (clientUrl) {
    jsSdk(clientUrl)
      .then(function (obj) {
        res.json(obj)
      })
  } else {
    res.end('no url')
  }
}

module.exports = routerSdk
```
以上基本就完成了后端返回签名的过程(省略了redis部分)。具体细节可参考我当时的[练手项目](https://github.com/Mr-six/wx-test/tree/api_sign/server/src/wx)中的代码。
至此，前端就可以使用jssdk来完成功能的调用了。
ps:某次使用录音接口做了一个功能，但是发现，微信服务器只会保存3天数据，需要自己下载到自己的服务器才行，不知道诸位有没做过类似的需求，给我提供下指导啥的，感激不尽~
## 后记

后来又写过一个获取用户信息的页面，感觉也是挺常用的就写个demo出来看看吧(没有做access_token的保存，好像是没有获取次数限制)。
```
router.get('/', function(req, res, next){
  console.log("oauth - login")

  // 第一步：用户同意授权，获取code
  let router = 'get_wx_access_token'
  // 这是编码后的地址
  let return_uri = encodeURIComponent(base_url + router)
  console.log('回调地址:' + return_uri)
  let scope = 'snsapi_userinfo'

  res.redirect('https://open.weixin.qq.com/connect/oauth2/authorize?appid='+appid+'&redirect_uri='+return_uri+'&response_type=code&scope='+scope+'&state=STATE#wechat_redirect')
})

// 第二步：通过code换取网页授权access_token
router.get('/get_wx_access_token', function(req,res, next){
  console.log("get_wx_access_token")
  console.log("code_return: "+req.query.code)
  let code = req.query.code
  request.get(
    {   
      url:'https://api.weixin.qq.com/sns/oauth2/access_token?appid=' + appid + '&secret=' + appsecret+'&code=' + code + '&grant_type=authorization_code',
    },
    function(error, response, body){
      if(response.statusCode === 200){

        // 第三步：拉取用户信息(需scope为 snsapi_userinfo)
        // console.log(JSON.parse(body))

        let data = JSON.parse(body)
        let access_token = data.access_token
        let openid = data.openid

        request.get(
          {
            url:'https://api.weixin.qq.com/sns/userinfo?access_token='+access_token+'&openid='+openid+'&lang=zh_CN',
          },
          function(error, response, body){
            if(response.statusCode == 200){

              // 第四步：根据获取的用户信息进行对应操作
              let userinfo = JSON.parse(body)
              console.log(JSON.parse(body))
              console.log('获取微信信息成功！')

              小测试，实际应用中，可以由此创建一个帐户
              res.send("\
                  <h1>"+userinfo.nickname+" 的个人信息</h1>\
                  <p><img src='"+userinfo.headimgurl+"' /></p>\
                  <p>"+userinfo.city+"，"+userinfo.province+"，"+userinfo.country+"</p>\
              ")

            }else{
              console.log(response.statusCode)
            }
          }
        )
      }else{
        console.log(response.statusCode)
      }
    }
  )
})
```
