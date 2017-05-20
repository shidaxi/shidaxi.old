---
layout: posts
title:  "command mysqladmin usage"
date:   2015-09-01 08:00
categories: mysql cmd
permalink: /tech/command-mysqladmin-usage
---

官方手册页：http://dev.mysql.com/doc/refman/5.6/en/mysqladmin.html

mysqladmin 也是一个 mysql 的客户端，它的功能大部分都可以用 mysql 来实现，只是它将一些常用管理命令简化成子命令，让你无需拼写复杂的 sql 语句了。

Step 1，创建一个管理帐号，并赋予相应的权限，用于执行管理命令

```bash

$ mysql -u root -p
mysql> create user 'shixi'@'%' identified by 'shixi~~';
mysql> grant super,reload,shutdown,process,create,drop,replication client,create user on *.* to 'shixi'@'%';
mysql> flush privileges

```
Step 2, 为了不用每次输入密码，将 mysql 的用户名和密码写进配置文件，将权限设置为600(只有你自己可读).如果你用mysql-5.6，里面有一个神器 mysql-config-editor 来生成一个加密的~/.mylogin.cnf

```bash
$ cat ~/.my.cnf
[mysqladmin]
user=shixi
password=shixi~~

$ chmod 600 ~/.my.cnf

```

step 3, 可以进行操作啦

```bash

# CREATE/DROP 数据库
$mysqladmin create testdb
$mysqladmin drop testdb

# 查看 server 状态
$mysqladmin status
# 你也可以这样简化输入,前提是不能跟其他命令简写冲突
$mysqladmin stat
# 但是你不能像下面这样,因为有另外一个命令 start-slave 前三个字母也是 sta，这样就冲突了
$mysqladmin sta

# 查看 server 状态变量和变量值
$mysqladmin extended-status
# 同样可以简写为
$mysqladmin ext  
#甚至这样
$mysqladmin e

# flush 系列子命令
$mysqladmin flush-hosts       #刷新主机缓存
$mysqladmin flush-logs        #关闭并重新打开日志文件，或者有时轮转日志文件
$mysqladmin flush-privileges  #刷新授权缓存中，一般权限变更之后都会刷一把，保证立刻生效
$mysqladmin flush-status      #重置部分变量值
$mysqladmin flush-tables      #关闭所有打开的表并且清空查询缓存
$mysqladmin flush-threads     #刷新线程的缓存

# refresh 相当于把以上 flush 全部做一边
$mysqladmin refresh
$mysqladmin ref

# 重新加载授权表，同 flush-privileges
$mysqladmin reload
$mysqladmin rel

# 将帐号 abc 的密码从 ABC 改为 DEF
$mysqladmin -u abc -pABC password 'DEF'

# 查看当前进程
$mysqladmin processlist
$mysqladmin pr

# 杀死某个，某几个进程，多个进程 id 使用逗号隔开，id 通过上个命令(pr)得到
$mysqladmin kill id1,id2,id3...

# 检查服务器是不是还活着
$mysqladmin ping
$mysqladmin pi

# 安全地关闭 mysqld 服务
$mysqladmin shutdown
$mysqladmin sh

# 查看系统变量值
$mysqladmin variables
$mysqladmin va

# 让 slave 启动或者停止复制
$mysqladmin start-slave
$mysqladmin stop-slave

# 查看版本
$mysqladmin version
$mysqladmin ver


#一次执行多个命令,用空格分开，依次罗列多个命令或者其简写
$mysqladmin pro ver stat

```
