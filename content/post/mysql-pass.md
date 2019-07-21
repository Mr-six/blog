---
title: 'mysql5.7 + 默认密码的问题'
date: 2016-05-05 12:23:49
categories: ['数据库']
tags: ['mysql']
---
ps: 当时这个坑给我留下了阴影，至此，电脑的mysql一直使用的是docker版本😁

1.从官网下载mysql-5.7.xx-winx64.zip 我用的是目前最新的5.7.12

新版本初始登录密码不再是空的，而是随机生成一个密码在什么mysql_secret文件里面，可是找不到这个文件啊
然后就坑啦啊啊啊啊啊啊啊~~~~
下面是我的解决方法：

在安装录下复制my-default.ini 为my.ini，在my.ini文件中，加入：skip-grant-tables（不需要密码验证，直接登录）

因为5.7自动生成root密码 然而这个密码你并不知道


2.转至bin目录下 安装bin\mysqld install


3.初始化data目录，bin\mysqld --initialize 
这是最重要的一步 。由于某种原因吧，因为现在解压出来的文件是没有data 文件夹的 需要mysqld --initialize初始化data目录 
没有data文件你是无法启动mysql的



4.net start mysql启动mysql



5.在bin目录下输入：mysql -uroot -p 进入mysql控制台 因为前面设在my.ini文件中，加入：skip-grant-tables 所以不需要输入密码 直接回车即可


6.接下来要做的就是把密码改掉。因为之前的密码你不知道。（顺便求获取密码的方法。。。。）
use mysql ，使用mysql数据库
修改root用户密码：
```
UPDATE user SET authentication_string= password ('123456') WHERE User='root';
```
输入quit;
退出mysql控制台，重新启动mysql，现在就可以用root和你的新密码登录了。


还有一个密码不过期配置：在my.ini 中加入：default_password_lifetime=0 ，设置为：0 表示密码永不过期


以下为我的mac版：
第一步进行完之后，
```
- 在命令行输入mysql可直接进入，
  
- use mysql    ——启用mysql数据表

- UPDATE user SET authentication_string= password ('123456') WHERE User='root';    ----修改root密码

- quit;         ------退出

- 去掉my.cnf中的   skip-grant-tables（不需要密码验证，直接登录）
加上default_password_lifetime=0 ，设置为：0 表示密码永不过期
```
