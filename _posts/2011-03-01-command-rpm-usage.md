---
layout: posts
title:  "Command RPM Usage"
date:   2011-03-01 08:00
categories: redhat linux rpm
permalink: /tech/command-rpm-usage
---

RPM,即Redhat Package Manager。
rpm命令参数有这么几种(只列常用)：

操作：增(-i|–install), 删(-e|–erase),改(升级 -U|–upgrade),查(-q|–query, -V|–verify, -K|–checksig)，及其他

选择项：-a|–all, -f|–file, -g|–group, -p|–package

查询项：-d|–docfiles, -i|–info, -l|–list, -R|–requires, -s|–state

软件包管理，无外乎增删改查四种操作，下面一一演示

增，即安装,-i(nstall)。成功不会提示，为了知道发生了什么，加-v(bose) -h(ash)参数

```bash

$ sudo rpm -i cowsay-3.03-4.el5.noarch.rpm
$ sudo rpm -ivh cowsay-3.03-4.el5.noarch.rpm
Preparing...                ########################################### [100%]
   1:cowsay                 ########################################### [100%]
   

#从URL安装
$sudo rpm -ivh http://dl.fedoraproject.org/pub/epel/5/i386/cowsay-3.03-4.el5.noarch.rpm

```

删，即卸载,-e(rase)。成功不会有提示，失败会有错误

```bash

$ sudo rpm -e cowsay

```

改，升级, -U(pgrade)。一般升级软件使用yum

```bash

$ sudo rpm -Uvh cowsay-3.03-4.el5.noarch.rpm

```

查，-q(uery)。即根据各个条件来查询,这个操作用的最多

```bash 

#-a(ll)系统安装了哪些包?
$ rpm -qa

#系统安装了哪些PHP相关的包？
$ rpm -qa | grep php
php53-common-5.3.3-13.el5_9.1
php53-5.3.3-13.el5_9.1
php53-pdo-5.3.3-13.el5_9.1
php53-mysql-5.3.3-13.el5_9.1
php53-cli-5.3.3-13.el5_9.1

#我安装cowsay了吗？
$ rpm -qa cowsay

#-l(ist)我安装的cowsay都有哪些文件分别装在了哪些位置
$ rpm -ql cowsay

#我安装的cowsay里面有哪些命令（可执行程序）
$ rpm -ql cowsay | grep /bin/
/usr/bin/animalsay
/usr/bin/cowsay
/usr/bin/cowthink

#-f(ile)查询ls命令属于哪个包？
$ rpm -qf `which cowsay `
cowsay-3.03-4.el5.noarch.rpm

#-d(ocfile)查询软件包的文档都安装在哪里了
$ rpm -qd cowsay
/usr/share/doc/cowsay-3.03/ChangeLog
/usr/share/doc/cowsay-3.03/LICENSE
/usr/share/doc/cowsay-3.03/README
/usr/share/man/man1/cowsay.1.gz
/usr/share/man/man1/cowthink.1.gz

#-i(nfo)查看已安装包的详细信息（-i与-q一起用就是info，单独用就是install）
$ rpm -qi cowsay

#-R(equires)查询已安装包的依赖
$ rpm -qR cowsay
/bin/sh  
/usr/bin/perl  
perl(Cwd)  
perl(File::Basename)  
perl(Getopt::Std)  
perl(Text::Tabs)  
perl(Text::Wrap)  
rpmlib(CompressedFileNames) <= 3.0.4-1
rpmlib(PayloadFilesHavePrefix) <= 4.0-1

#-s(ate)查看已安装包的各个文件状态
$ rpm -qs cowsay

#-p(ackage)指定软件包
$ ls cowsay-3.03-4.el5.noarch.rpm
cowsay-3.03-4.el5.noarch.rpm

#下面命令输出同上面的
$ rpm -qdp cowsay-3.03-4.el5.noarch.rpm
$ rpm -qip cowsay-3.03-4.el5.noarch.rpm
$ rpm -qlp cowsay-3.03-4.el5.noarch.rpm
$ rpm -qRp cowsay-3.03-4.el5.noarch.rpm
$ rpm -qsp cowsay-3.03-4.el5.noarch.rpm

#-K校验包的gpg签名，防止包是被篡改的或者破损的
$rpm -K cowsay-3.03-4.el5.noarch.rpm 
cowsay-3.03-4.el5.noarch.rpm: (sha1) dsa sha1 md5 gpg OK


#-V(rify)将已安装的包和rpm数据库做比对
$sudo rm /usr/share/man/man1/cowsay.1.gz
$rpm -V cowsay
missing   d /usr/share/man/man1/cowsay.1.gz

```
