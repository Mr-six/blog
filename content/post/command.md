---
title: '常用'
date: 2020-06-23T22:35:06+08:00
categories: ['linux']
tags: ['命令']
---

### git 相关

#### 记住密码

1. 长期储存
    ```
    git config --global credential.helper store
    ```
1. 如果想自己设置失效时间 1小时：
    ```
    git config credential.helper 'cache --timeout=3600'
    ```
1. 删除记住的密码(~/.gitconfig 中保存着 git 密码可以删除)
    ```
    git config --global --unset credential.helper
    ```

#### 分支相关
1. 以远程分支为基础建立分支
    ```
    git checkout -b branchname remote/branchname 
    
    ```
1. 分支中删除某项提交参考
    ```
    git reset HEAD~2 （分支向后回退两个提交，丢弃了之前的commit）
    git revert HEAD~2 （撤销一个提交的同时，会创建一个新的提交，不会重写提交历史）
    ```
1. 以远程分支为基础建立分支
    ```
    git checkout -b branchname remote/branchname 
    ```
1. 本地分支追踪远程分支
    ```
    git branch --set-upstream-to=origin/master
    ```
1. 本地强制使用远端分支
    ```
    git fetch
    git reset --hard origin/master
    ```

### linux 服务器

#### 端口相关

1. 查看远程机器某个端口是否打开
    ```
    telnet ip prot
    telnet 11.230.29.100 3005 (ctrl + ] 退出)
    ```
1. 查看本机是打开的端口
    ```
    netstat –apn
    netstat –apn | grep 3005
    ```
1. 查看端口占用
    ```
    sudo lsof -i:prot
    sudo lsof -i:3005
    ```

#### 进程相关
1. 查看程序占用
    ```
    ps -aux | grep tomcat
    ```
1. 查看某个pid进程
    ```
    ll /proc/{pid}
    cwd 符号链接的是进程运行目录；
    exe 符号连接就是执行程序的绝对路径；
    cmdline 就是程序运行时输入的命令行命令；
    environ 记录了进程运行时的环境变量；
    fd 目录下是进程打开或使用的文件的符号连接。
    ```
1. docker 停止所有容器, 删除容器,删除images
    ```
    docker stop $(docker ps -a -q)      # 停止容器
    docker rm $(docker ps -a -q)        # 删除容器
    docker rmi $(docker images -a -q)   # 删除所有镜像
    ```

#### ssh

1.  免密登录
    ```
    ssh-keygen -t rsa # 生成 公钥和私钥
    ssh-copy-id -p 1234 user@192.0.0.1
    ```
1. ssh 创建 config 文件 ssh name 即可登录
    ```
    新建/编辑 ~/.ssh/config 文件

    Host name
        HostName 192.0.0.1
        Port 22
        User user
        IdentityFile ~/.ssh/id_rsa
        IdentitiesOnly yes
    ```
1. ssh 连接数限制
    ```
    /etc/ssh/sshd_config 文件中  
    MaxStartups 默认设置是 10:30:60，意思是从第10个连接开始以30%的概率（递增）拒绝新连接，直到连接数达到60为止
    可将其修改为： 100:30:1000
    /etc/init.d/ssh restart

    查看22端口连接数：
    netstat -nat|grep -i '22' |wc -l
    ```

#### tmux

1. window
    ```
    Ctrl+b c 新建窗口
    Ctrl+b , 重命名窗口
    Ctrl+b p 上一个窗口
    Ctrl+b n 下一个窗口
    Ctrl+b & 关闭窗口
    ```

1. pane
    ```
    Ctrl+b % 左右拆分
    Ctrl+b { 左移
    Ctrl+b } 右移

    
    Ctrl+b " 上下拆分
    Ctrl+b Ctrl+o 下移
    Ctrl+b Alt+o 上移

    Ctrl+b z 全屏/恢复
    Ctrl+b x 关闭 pane
    ```