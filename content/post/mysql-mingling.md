---
title: 'mysql基础命令'
date: 2016-05-15 11:09:33
categories: ['数据库']
tags: ['mysql']
---
作为前端虽然数据库接触的不多，但当使用时总记不住命令确实有点尴尬，遂做此笔记，以方便查阅。
## 登录登出
```
mysql -h localhost -u root -p  //登录
QUIT  // 登出
```
## 基本操作
ps: 又一次储存emoji表情时，出现了一次报错，因为UTF-8编码有可能是两个、三个、四个字节，其中Emoji表情是4个字节，而Mysql的utf8编码最多3个字节，所以导致了数据插不进去。
当时的解决方式是更改了某个表的字符集
```
ALTER TABLE tab-name CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```
注释：utf8mb4的最低mysql版本支持版本为5.5.3+
* 数据库操作
  * 创建数据库
    * `CREATE DATABASE name CHARSET SET utf8;`
  * 修改数据库
    * `ALTER DATABASE name CHARACTER SET utf8;`
  * 查看创建数据库语句
    * `SHOW CREATE DATABASE name`
  * 删除数据库
    * `DROP DATABASE name;`
  * 使用数据库
    * `USE database-name`
  * 显示当前选择
    * `SELECT DATABASE();`

* 数据表操作
  * 创建数据表：
    ```
     CREATE TABLE name (
      column-name data_type,
      ...
      )
    ```
  * 查看次数据表创建语句：
      * `SHOW CREATE TABLE tab-name`
  * 查看次数据库中的数据表：
      * `SHOW TABLES;`
  * 查看次数据库中的数据表：
      * `DESC tab-name;`
  * 查看数据列：
      * `SHOW COLUMS FROM tab-name;`
  * 插入数据：
     * `INSERT tab-name [(column1,column2...)] VALUES (val1,val2...)`
  * 更新数据：
    * `UPDATE tab-name SET column1 = val1 , column2 = val2 [WHERE expr = val]`
  * 外键添加：
    * `FOREING KEY (column) REFERENCES tab-name (column)`
  * 查看数据：
      * `SELECT * FROM tab-name;`
      * 查看约束 `SHOW INDEXES FROM tab-name`
      * 过滤&排序 [`WHERE`,`GROUP BY`(分组)，`HAVING`,`ORDER BY`,`LIMIT`]()
  * 修改数据表：
    1.  添加列：`ALTER TABLE tab-name ADD column data-type [FIRST | AFTER column-name]`(添加多列-add后跟小括号，但无位置关系)
    *  删除列：`ALTER TABLE tab-name DROP column-name`
    * 添加约束： `ALTER TABLE tab-name PRIMARY KEY (column-name)`
    * 删除主键约束：`ALTER TABLE tab-name DROP PRIMARY KEY`
    * 删除唯一约束：`ALTER TABLE tab-name DROP {INDEX | KEY} index-name`
    * 添加删除默认约束：`ALTER TABLE tab-name ALTER column-name {SET DEFAULT default | DROP DEFAULT}`
    * 数据表变更1：`ALTER TABLE tab-name MODIFY column-name data-type [FIRST | AFTER column-name]`
    * 数据表变更2：`ALTER TABLE tab-name CHANGE old-name new-name data-type [FIRST | AFTER column-name]`
  * 清空数据表：
    * `DELETE FROM tab-name;`
    * `TRUNCATE TABLE tab-name`
  * 删除数据表：
    * `DROP tab-name`

数据表均用到了的为sql语句查询，[语句参考](http://www.tutorialspoint.com/sql/sql-syntax.htm)

## 账户权限管理
查看账户权限：
```
SHOW GRANTS; # 当前用户权限
SHOW PRIVILEGES;  # mysql 所支持的权限列表
SHOW GRANTS FOR user;  # 查看某用户
```
grant 权限名称[字段列表] on [数据库资源类型]数据库资源 to MySQL账户1,[MySQL账户2] [with grant option]
>\*.* 表示所有数据库，所有数据表

>'user'@'%' user 账号可以在任意的主机上进行登录。

### 创建服务实例级账号
```
grant all privileges on *.* to 'supperuser'@'%' identified by 'password' with grant option;
```
将创建一个名字为supperuser的账号，拥有所有的数据库权限，并且具有grant 权限，可以创建其他拥有服务实例权限的其他用户。
创建数据库实例账号

### 创建数据库实例账号

```
grant all privileges on database_name.* to 'user'@'%' identified by 'userpass' with grant option;
```
将创建一个名字为user的账号。拥有database_name 数据库的所有权限，可以随该库中的表进行所有操作。

### 创建数据表级别的账号
```
grant all privileges on table tab_name.test to 'user'@'%' identified by 'userpass' with grant option;
```
将创建一个名字为user 的用户，对tab_name数据库中test拥有所有的权限。
### 权限更改
想要增加update, delete,alter 权限可以如下操作：
```
grant update,delete,alter on database_name.* to 'user'@'%' identified by 'userpass' with grant option;
```
想要移除insert 权限：
```
revoke insert on database_name.* from  'user'@'%';
```
## 用户管理
删除用户：
```
drop user user_name
```
[参考文章roverliang](http://www.cnblogs.com/roverliang/p/6444512.html)
