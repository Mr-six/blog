---
title: '奇技淫巧（不断更新中）'
date: 2016-08-25 11:43:48
categories: ['其他']
tags: ['技巧']
---

## git
*当我们使用git branch -D 删除一个本地分支后，怎样才能恢复呢？*
操作 :
```
git log -g       // 显示历史提交，找到分支对应的最后一笔提交的commit-id
git checkout -b {branch-name} {commit-id}         // 以指定commit为基础创建分支
```
*git记住密码*
1.长期储存
```
git config --global credential.helper store
```
2. 如果想自己设置失效时间--1小时：
```
git config credential.helper 'cache --timeout=3600'
```
*以远程分支为基础建立分支*
```
git checkout -b branchname remote/branchname 
```
*分支中删除某项提交(参考)[https://github.com/geeeeeeeeek/git-recipes/wiki/5.2-%E4%BB%A3%E7%A0%81%E5%9B%9E%E6%BB%9A%EF%BC%9AReset%E3%80%81Checkout%E3%80%81Revert%E7%9A%84%E9%80%89%E6%8B%A9]*
```
git reset HEAD~2 （分支向后回退两个提交，丢弃了之前的commit）
git revert HEAD~2 （撤销一个提交的同时，会创建一个新的提交，不会重写提交历史）
```
*清理工作目录*
```
git reset --hard  (清空所有未提交的内容)
git clean -xdf （清除未追踪的问价或文件夹）
```

## iterm
- sublime 命令行打开文件：
将如下代码插入到 .zshrc 文件尾部
```
alias subl="'/Applications/Sublime Text.app/Contents/SharedSupport/bin/subl'"
alias sub="subl"
export EDITOR="subl"
```
- 正确的下载 node-sass的姿势
```
SASS_BINARY_SITE=https://npm.taobao.org/mirrors/node-sass/ npm i -D node-sass
```
- 配置iterm 2 代理

因为本地使用ssr 故将代理设置为本地的代理即可 ssr -> http 代理设置 为1087端口
编辑~/.zshrc 写入

## proxy
```
alias proxy='export http_proxy=http://127.0.0.1:1087 https_proxy=http://127.0.0.1:1087'  
alias disproxy='unset http_proxy https_proxy'
```
即可用命令开启或者关闭命令行的代理

## es6
- 解构赋值
数组
```
let [head, ...tail] = [1, 2, 3, 4, 5, 6]
// head 1
// tail [2, 3, 4, 5, 6]
```
对象-数组
```
let arr = [1, 2, 3, 4, 5, 6]
let {0: head, [arr.length - 1]: tail} = arr
// head 1
// tail 6
```
对象-对象
```
let obj = {first: 'hello', last: 'world'}
let {first: f, last: l} = obj
// f 'hello'
// l 'world'
// first undefined
// last undefined
// 真正被赋值的是f 和l 而不是 模式 first 和last
```
