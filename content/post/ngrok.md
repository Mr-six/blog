---
title: 'ngrok内网穿透服务器搭建及配置（附docker和Nginx配置）'
date: 2017-03-29 12:22:49
categories: ['linux']
tags: ['网络']
---
##前言
内网穿透，无需多言，用处多多。首先强力推荐一款[Sunny大大搭建的ngrok服务](https://www.ngrok.cc/)好用的不行，而且有免费选项，感觉平时够用了。那么，为什么自己还要搭建一个呢？可能刚刚看过两眼的docker入门，想来尝试一下吧。恩，也可能是闲的吧┑(￣Д ￣)┍


----------


ps: ngrok 2版本已商业化，开源的只到1.7版本，听说会有些性能问题（然而我一小白并不懂😭）。还有一款基于go的开源的内网穿透工具[frp](https://github.com/fatedier/frp) 感觉很厉害的样子，不过我觉得等以后能看懂了在来玩吧/(ㄒoㄒ)/~~
##搭建过程
至于玩法，我懂得就很少了，可能以后有时间了深入了解下。现在也就映射下本地页面啥的😂。
还是直接上实在的吧（以下命令基于Ubuntu）：
###准备
 - 域名
    - 域名可购买略
    - 域名解析设置
 - 服务器

    - 所需软件安装
    - 克隆源码
    - 生成证书
    - 编译生成服务端软件 和 客户端软件
 
 - 容器化
    - 生成镜像
    - 利用docker-compose 集成Nginx反向代理
###具体步骤

####域名解析设置
   - 假设你的域名为： yourset.com
   - 假设你的ngrok服务二级访问域名为：ngrok.yourset.com
   - 添加如下解析：ngrok.yourset.com --->A记录 your ip
   - *.ngrok.yourset.com --->CNAME 到 ngrok.yourset.com（可自由配置）
####服务环境设置

- 当时编译时是在一台32位 Ubuntu 的搬瓦工vps上搭建的环境，而现在的运行环境是64位 Ubuntu 的[vultr vps](http://www.vultr.com/?ref=7123461)上运行的。因为搬瓦工的vps上不能愉快玩docker。决定以后不再续费了，但上面已经安装好了go 和git所以就顺势搞了，然后推到了GitHub上，想再使用dockerhub的自动构建功能，生成了image，然后就华丽丽的报错了，构建失败（后方有错误代码）。只好用另一台拉取了代码构建的镜像(啊~能不能好好玩耍了 (╯‵□′)╯︵┻━┻)
- 安装git
```
$ sudo apt-get update
$ sudo apt-get install git
```
- 安装[go lang](https://golang.org/dl/)
```
$ https://storage.googleapis.com/golang/go1.8.linux-386.tar.gz(注意对应自己的系统下载，我当时用的是32位，具体请参考上方连接）
$ tar -C /usr/local -xzf go1.8.linux-386.tar.gz 
$ vi /root/.bashrc #根据你的用户选取
$ export PATH=$PATH:/usr/local/go/bin #添加环境变量
$ source /root/.bashrc #更新环境变量
```
        
#### 克隆源码
```
$ cd /usr/local
$ git clone https://github.com/inconshreveable/ngrok.git
```
#### 引入环境变量
```
$ export GOPATH=/usr/local/ngrok/    #目录位置
$ export NGROK_DOMAIN="ngrok.yourset.com"   #你的ngrok服务二级域名
```
#### 根据你的域名生成证书
```
openssl genrsa -out rootCA.key 2048
openssl req -x509 -new -nodes -key rootCA.key -subj "/CN=$NGROK_DOMAIN" -days 5000 -out rootCA.pem
openssl genrsa -out server.key 2048
openssl req -new -key server.key -subj "/CN=$NGROK_DOMAIN" -out server.csr
openssl x509 -req -in server.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out server.crt -days 5000
```
#### 证书替换
```
cp rootCA.pem assets/client/tls/ngrokroot.crt
cp server.crt assets/server/tls/snakeoil.crt
cp server.key assets/server/tls/snakeoil.key
```
#### 客户端版本说明
- Linux 平台 32 位系统：GOOS=linux GOARCH=386
- Linux 平台 64 位系统：GOOS=linux GOARCH=amd64

- Windows 平台 32 位系统：GOOS=windows GOARCH=386
- Windows 平台 64 位系统：GOOS=windows GOARCH=amd64

- MAC 平台 32 位系统：GOOS=darwin GOARCH=386
- MAC 平台 64 位系统：GOOS=darwin GOARCH=amd64

- ARM 平台：GOOS=linux GOARCH=arm
    

编译服务端的 ngrokd(客户端和服务端生成的文件均在/usr/local/ngrok/bin下)
    $ GOOS=linux GOARCH=amd64 make release-server  #根据平台选取
编译客户端 ngrok（可生成压缩包下载到本地然后放到网盘存起来😝方便以后使用）
    $ GOOS=darwin GOARCH=amd64 make release-client  #mac 64
    $ GOOS=windows GOARCH=amd64 make release-client  #win64
    $ GOOS=linux GOARCH=amd64 make release-client  #linux64
    $ GOOS=linux GOARCH=arm make release-client  #arm平台如树莓派
#### 开启服务端（设置端口是请注意端口是否打开，以及防火墙配置）
```
$ /usr/local/ngrok/bin/ngrokd -domain="ngrok.yourset.com" -httpAddr=":2443" -httpsAddr=":3443" -tunnelAddr=":4443"
```
参数说明：
    -domain 访问ngrok是所设置的服务地址生成证书时那个
    -httpAddr http协议端口 默认为80
    -httpsAddr https协议端口 默认为443 （可配置https证书）
    -tunnelAddr 通道端口 默认4443
    
#### 启动客户端-使用命令行参数（以mac为例）
将客户端放到自己喜欢的位置并在当前目录下创建配置文件
```
$ vi ngrok.yml
```
写入如下内容
```
server_addr: "ngrok.yourset.com:4443"
trust_host_root_certs: false
```
启动客户端命令
```
$ ./ngrok -config=./ngrok.yml -proto=http -subdomain=test 8080 #启动http转发
$ ./ngrok -config=./ngrok.yml -proto=tpc 22  #启动tcp转发 
```
本地22端口，远程随机暴露大端口
参数说明：
    trust_host_root_certs #是否信任系统根证书，如果是带自签名证书编译的 ngrok 客户端，这个值应该设置为 false；如果使用 CA 证书，或者用户添加了根证书，这个值应该设置为 true
    -proto     #转发协议 不指定默认是 http+https
    -subdomain #访问本地时的三级域名 不指定就会随机生成 tcp不支持此参数
    8080       #本地服务的端口号
    -config    #指定配置位置，默认为$HOME/.ngrok2/ngrok.yml
        
#### 启动客户端-使用配置文件
在ngrok中写入如下内容：
```
server_addr: "ngrok.yourset.com:4443"
    trust_host_root_certs: false
    tunnels:
      web:
        proto:
          http: "3000"
      web2:
        proto:
          http: "8080"
      tcp:
        proto:
          tcp: "3022"
        remote_port: 4444
      tcp2:
        proto:
          tcp: "22"
        remote_port: 4445
```
启动单个服务如 
```
$ ./ngrok -config=./ngrok.yml start web  #启动web服务 使用的前缀域名为web
$ ./ngrok -config=./ngrok.yml start tcp  #启动tcp服务 使用远程端口3022
```
启动多个服务：
```
$ ./ngrok -config=./ngrok.yml start web tcp  #同时启动两个服务
$ ./ngrok -config=./ngrok.yml start-all  #启动所有服务
```
![图片描述][1]

### docker 配置 和 Nginx配置
基本搭建到上面基本就能用了，但是对于暴露80端口的问题这时就需要使用Nginx做一个端口的反向代理。
而加入使用的vps更换了话，就要重新再来搭建一遍，想想用docker的话，那不就很方便吗。（当初想过做成shell脚本，发现有点难/(ㄒoㄒ)/~~，还是没学会几个命令）

由于docker的一些东西和ngrok的配置关系不是很大了，也比较简单，没有必要重新写出来啦，具体参考我的[gayhubs](https://github.com/Mr-six/mydocker)上的简单配置

使用docker时遇到的一个问题
  - ngrok 只暴露4443 通信端口，而其他的端口通过 docker内部的端口暴露给Nginx容器端口做反代时，发现客户端连接不上（可能是我没配置好%>_<%）

## 后记
我只是个小白，好多东西都是一知半解。有什么说的不对的，望多多指教。接下来想把shadowsocks也放到docker中，可是多用户端口暴露的问题还不知道如何解决。一次看到了shadowsocksR有一个单端口多用户的功能，看来以后可以折腾一下啦~\(≧▽≦)/~


    
    
    
    


  [1]: https://segmentfault.com/img/bVLpF7?w=1420&h=496
