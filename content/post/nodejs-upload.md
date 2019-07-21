---
title: 'nodejs 文件上传演示'
date: 2017-04-10 12:22:49
categories: ['js']
tags: ['js']
---
>如何接收和处理前端发送的数据？

这是后端API实现的基础，当前端通过get或者post发送来的数据，express要如何接收

##req.query 解析get参数
返回解析过的请求参数对象，默认为 {} 
获取get方法发送过来的参数
来个栗子：
```
// GET /search?name=xiaoming
req.query.name
// => "xiaoming"
// GET /shoes?order=desc&shoe[color]=blue&shoe[type]=converse
req.query.order
// => "desc"
req.query.shoe.color
// => "blue"
req.query.shoe.type
// => "converse"
```

## req.body 解析post参数
返回post解析过的参数对象，默认为 undefined 解析需要引入 body-parser 中间件。
```
const app = require('express')()
const bodyParser = require('body-parser')
app.use(bodyParser.urlencoded({ extended: false }))  // parse application/x-www-form-urlencoded
app.use(bodyParser.json())  // parse application/json

app.post('/info', function (req, res){
  if (req.body) {
    db.saveInfo(req.body)
  }
})

// POST /info {name: "xiaoming",pass: "passwd"}
req.body
// => {name: "xiaoming",pass: "passwd"}
```
## req.cookies 解析发送的cookies
同样，需使用  cookie-parser 中间件，
```
// Cookie: name=xiaoming
req.cookies.name
// => "xiaoming"
```

## req.params 解析请求中 包含在route中的参数
比如 当route中有 /user/:name
```
// GET /user/xiaoming
req.params.name
// => "xiaoming"
```
----
> 以上均处理的是的文本形式的文件，那如果是二进制文件（multipart/form-data 类型的表单数据）那就需要用到另一个中间件[multer](https://github.com/expressjs/multer)了
## multer 处理文件上传
```
var express = require('express')
var multer  = require('multer')
var upload = multer({ dest: 'uploads/' })

var app = express()

app.post('/profile', upload.single('avatar'), function (req, res, next) {
  // req.file 是 `avatar` 文件的信息
  // req.body 将具有文本域数据, 如果存在的话
}
```



