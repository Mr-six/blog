---
title: '我的搬瓦工VPS配置（个人）'
categories: ['vps']
date: 2017-05-10 15:49:51
tags: ['vps']
---
## 简介
由于之前在广州用的电信连接vultr不是很给力（尤其是晚上ping很高，不稳定）所有打算到了北京再重新开通，谁知2.5刀的断货了。感觉暂时用5刀的性价比不是很高。在寻找下一个实惠的vps时，发现了搬瓦工退出了kvm架构的vps（可以愉快地玩docker了\(^o^)/~）。遂顺手买了一个试试手。（配置过程略，安装的系统为Ubuntu16.04）
## 步骤一 开启 TCP BBR
Google开源TCP BBR拥塞控制算法（简称BBR）
具体细节我就不是很懂了看了几篇评测之后感觉还是不错的。
[bbr 一键开启地址（git）](https://github.com/teddysun/across/)
[使用方法](https://teddysun.com/489.html)
## 步骤二 安装docker
官方脚本（适用于Ubuntu 和 Debian）
        curl -sSL https://get.docker.com/ | sh
[其他系统安装](https://yeasy.gitbooks.io/docker_practice/content/install/)
### 安装docker-compose 
apt install docker-compose

安装docker之后，其他的服务和应用都可以基于docker来实现，也便于配置和管理。
## 步骤三 克隆git资源
将之前整理的docker应用及配置克隆到vps
```
git clone https://github.com/Mr-six/mydocker.git
```
根据需求开启docker服务即可。
