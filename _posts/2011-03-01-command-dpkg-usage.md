---
layout: posts
title:  "Command dpkg Usage"
date:   2011-03-01 08:00
categories: debian linux dpkg
permalink: /tech/command-dpkg-usage
---

dpkg即 Debian PacKaGe， 是Debian系列linux用的包管理器。 包管理器，无外乎增删改查四个操作，下面逐一演示

增，即安装，-i(nstall)

```bash

$ sudo dpkg -i cowsay_3.03+dfsg1-3_all.deb
Selecting previously unselected package cowsay.
(Reading database ... 151623 files and directories currently installed.)
Unpacking cowsay (from cowsay_3.03+dfsg1-3_all.deb) ...
Setting up cowsay (3.03+dfsg1-3) ...
Processing triggers for man-db ...

```

删，即卸载，-r(emove)

```bash

$ sudo dpkg -r cowsay
(Reading database ... 151684 files and directories currently installed.)
Removing cowsay ...
Processing triggers for man-db ...

```

改，升级， 不常用，一般用apt来升级

查，查询包信息


```bash

#-l(ist) 查询系统是否装了cowsay
$ dpkg -l cowsay
ii  cowsay                               3.03+dfsg1-3            all                     A configurable talking cow

#-L(istfiles)查询cowsay有哪些文件
$ dpkg -L cowsay

#-s(tatus)查询cowsay当前状态，也包含了部分包信息
$ dpkg -s cowsay
Package: cowsay
Status: install ok installed
Priority: optional
Section: games
Installed-Size: 280
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Architecture: all
Version: 3.03+dfsg1-3
Depends: perl
Suggests: filters
Description: A configurable talking Cowsay
Original-Maintainer: Francois Marier <francois@debian.org>
Homepage: http://www.nog.net/~tony/warez/cowsay.shtml

#-S(earch)查询cowsay命令属于哪个包
$ which cowsay
/usr/games/cowsay
$ dpkg -S /usr/games/cowsay
cowsay: /usr/games/cowsay
$ dpkg -S /bin/ls
coreutils: /bin/ls

```
