---
title: Debian系包管理工具apt笔记
date: '2016-10-10 10:01:18 -0800'
author: shidaxi
categories:
  - command
permalink: /posts/debian-apt-notes
---

本篇文章介绍Debian系Linux发行版\(Linux distro\)\(例如Debian/Ubuntu等\) 包管理工具apt的常用用法

#### 什么是apt、apt-get、apt-cache、以及dpkg？

Redhat系Linux发行版的管理工具\(Package Manager\)是yum+rpm，那么Debian系Linux发行版用的包管理工具则是apt+dpkg，可以用来执行安装、卸载、升级、查询、管理软件包等操作。

#### apt 与 dpkg 之间关系是什么？

dpkg的工作：针对某个软件包执行具体的安装、卸载、查询等操作

apt的工作：管理所有软件包，软件仓库\(repo\)，依赖\(dependencies\)等，调用dpkg执行具体的操作

#### apt工具包的命令工具有哪些，基本操作有哪些？

命令工具有这些：

```bash
$ dpkg -L apt | grep bin/
/usr/bin/apt-config
/usr/bin/apt-cdrom
/usr/bin/apt-get
/usr/bin/apt
/usr/bin/apt-cache
/usr/bin/apt-key
/usr/bin/apt-mark
```

基本操作:

```
# apt命令查询管理软件包，部分功能跟具体的 apt-get apt-cache 一样
$ apt
apt 1.2.10 (amd64)
Usage: apt [options] command

Most used commands:
  list - list packages based on package names
  search - search in package descriptions
  show - show package details
  install - install packages
  remove - remove packages
  autoremove - Remove automatically all unused packages
  update - update list of available packages
  upgrade - upgrade the system by installing/upgrading packages
  full-upgrade - upgrade the system by removing/installing/upgrading packages
  edit-sources - edit the source information file
  ...

# apt-get 管理包以及依赖包的安装、升级、卸载
$ apt-get
apt 1.2.10 (amd64)
Usage: apt-get [options] command
       apt-get [options] install|remove pkg1 [pkg2 ...]
       apt-get [options] source pkg1 [pkg2 ...]

Most used commands:
  update - Retrieve new lists of packages
  upgrade - Perform an upgrade
  install - Install new packages (pkg is libc6 not libc6.deb)
  remove - Remove packages
  purge - Remove packages and config files
  autoremove - Remove automatically all unused packages
  dist-upgrade - Distribution upgrade, see apt-get(8)
  dselect-upgrade - Follow dselect selections
  build-dep - Configure build-dependencies for source packages
  clean - Erase downloaded archive files
  autoclean - Erase old downloaded archive files
  check - Verify that there are no broken dependencies
  source - Download source archives
  download - Download the binary package into the current directory
  changelog - Download and display the changelog for the given package
  ...

# apt-cache 查询展示包信息
$ apt-cache
apt 1.2.10 (amd64)
Usage: apt-cache [options] command
       apt-cache [options] show pkg1 [pkg2 ...]

Most used commands:
  showsrc - Show source records
  search - Search the package list for a regex pattern
  depends - Show raw dependency information for a package
  rdepends - Show reverse dependency information for a package
  show - Show a readable record for the package
  pkgnames - List the names of all packages in the system
  policy - Show policy settings
  ...
```

#### 如何查找某个软件包\(例如cowsay\)?

```bash
$ apt search cowsay
Sorting... Done
Full Text Search... Done
cowsay/xenial,xenial 3.03+dfsg1-15 all
  configurable talking cow

cowsay-off/xenial,xenial 3.03+dfsg1-15 all
  configurable talking cow (offensive cows)

presentty/xenial 0.2.0-1 amd64
  Console-based presentation software

xcowsay/xenial 1.3-1 amd64
```

#### 如何查看某个包信息\(例如cowsay\)？

```bash
$ apt show cowsay
Package: cowsay
Version: 3.03+dfsg1-15
Priority: optional
Section: universe/games
Origin: Ubuntu
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Original-Maintainer: Tony Maillefaud <maltouzes@gmail.com>
Bugs: https://bugs.launchpad.net/ubuntu/+filebug
Installed-Size: 92.2 kB
Depends: libtext-charwidth-perl, perl
Recommends: cowsay-off
Suggests: filters
Homepage: https://web.archive.org/web/20120527202447/http://www.nog.net/~tony/warez/cowsay.shtml
Download-Size: 18.0 kB
APT-Sources: http://cn.archive.ubuntu.com/ubuntu xenial/universe amd64 Packages
Description: configurable talking cow
 Cowsay (or cowthink) will turn text into happy ASCII cows, with
 speech (or thought) balloons. If you don't like cows, ASCII art is
 available to replace it with some other creatures (Tux, the BSD
 daemon, dragons, and a plethora of animals, from a turkey to
 an elephant in a snake).

```

#### 如何查看一个命令属于哪个包？

    $ dpkg -S `which ls`
    coreutils: /bin/ls

#### 如何查看一个包有哪些可执行文件？

```
$ dpkg -L coreutils | grep bin/
/bin/date
/bin/stty
/bin/mktemp
/bin/mv
...
```

#### 如何更新apt的包数据库/索引？

```
$ sudo apt-get update
```

#### 如何升级软件包？

```
$ sudo apt-get upgrade
```

#### 如何清理apt的包cache？

```

$ sudo apt-get autoclean
```

#### 如何只下载不安装？

```
$ sudo apt-get download 
```

#### 如何查看一个包的依赖有哪些？

```
$ apt-cache showpkg curl
```

#### 如何升级或者安装一个包？

```
$ sudo apt-get install cowsay
```

#### 如何同时升级或者安装多个包？

```
$ sudo apt-get install cowsay curl screen
```

#### 如何卸载一个包，但是保留配置文件？

```
$ sudo apt-get remove curl
```

#### 如何彻底卸载一个包？

```
$ sudo apt-get purge curl
```





参考：

> [https://www.tecmint.com/useful-basic-commands-of-apt-get-and-apt-cache-for-package-management/](https://www.tecmint.com/useful-basic-commands-of-apt-get-and-apt-cache-for-package-management/)
>
> [https://www.quora.com/What-is-the-difference-between-dpkg-and-apt-get](https://www.quora.com/What-is-the-difference-between-dpkg-and-apt-get)



