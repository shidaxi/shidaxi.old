---
title: 'openssh工具详解'
date: 2016-01-22 10:01:18 -0800
author: shidaxi
categories: [command]
permalink: /openssh-tools-notes
---

### SSH 相关物理文件

##### 客户端目录

Linux/Mac 系统的用户目录下一般有个目录  ~/.ssh 用于存放相关文件 例如密钥文件、配置文件等等

```bash

$ ls -1 ~/.ssh/ 
authorized_keys  # 授权公钥列表文件，内容是一个公钥的列表
authorized_keys2 # 同上，ssh v2专用
config           # 客户端配置文件
id_dsa           # DSA格式密钥的私钥文件(默认文件名)
id_dsa.pub       # DSA格式密钥的公钥文件(默认文件名)
id_rsa           # RSA格式密钥的私钥文件(默认文件名)
id_rsa.pub       # RSA格式密钥的公钥文件(默认文件名)
known_hosts      # 存放曾经连接过的server的public key

```

##### 服务端目录

### 认证方式
最常用的就是 密码认证 与 密钥认证

* 密码认证

就像从Linux的控制台登录一样，先输入一个账户名，再输入一个密码来完成认证。 

好处：方便；
坏处：账号容易被暴力破解；密码容易被猜到、泄露；无法免密码登录

* 密钥认证

通过在服务器上部署公钥，再持个人私钥进行认证。

好处：密钥认证比较安全；
坏处：需要部署密钥，略麻烦；


还有一些不常用的认证方式，暂时先不说。具体使用哪种认证方式ssh客户端与 sshd 服务端进程进行协商，一个一个尝试，直到认证成功



### 密钥对生成

通过ssh-keygen命令来生成一对密钥，命令行下输入 ssh-keygen回车，默认的类型是RSA，默认生成位置是 ~/.ssh/id_rsa，需要输入一个passphrase(有些地方翻译为 密语)，默认passphrase为空。

passphrase 不同于 password，它是用来对私钥文件进行加密的，原因很简单，如果你的私钥文件没有加密，万一文件泄露或者被同一主机上有超级权限的用户读取，那么就可以用它来冒充你登录主机。

```bash

[test1@new ~]$ ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/home/test1/.ssh/id_rsa): 
Created directory '/home/test1/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/test1/.ssh/id_rsa.
Your public key has been saved in /home/test1/.ssh/id_rsa.pub.
The key fingerprint is:
c6:cf:47:08:dc:ca:22:dd:8c:bb:db:89:32:3a:d3:ec test1@new

```

### OpenSSH 命令行实用工具

##### ssh


使用最广泛哪个的命令行ssh客户端，用法如下


```bash

$ ssh user@hostname   # 使用可以解析的hostname来登录

$ ssh user@ip         # 使用IP地址登录

$ ssh user@hostname -p 2222      # 如果是非标准22端口 使用 -p参数

$ ssh -l user hostname           # -l user 同 user@

$ ssh -i ~/.ssh/identity user@ip # 指定私钥文件，如果你的私钥文件名不是默认的id_rsa 

```


##### ssh-copy-id

先使用密码认证来部署公钥，以便下次使用密钥认证


```bash

# 部署公钥
$ ssh-copy-id -i ~/id_rsa user@ip  # 按提示输入user密码，认证成功即可部署好公钥

# 再使用密钥认证
$ ssh user@ip

```

##### ssh-keygen

用于生成密钥对

```bash

# 不指定参数，默认是 RSA算法
$ ssh-keygen 

# 指定-t dsa参数，使用DSA算法(不推荐,已被新版openssh淘汰)
$ ssh-keygen -t dsa

# 指定-b参数指定密钥长度，对于RSA密钥默认2048 bit长度，一般来说2048位即可兼顾安全与效率，太短强度不够，太长降低加密效率
$ ssh-keygen -b 2048

# -C 指定Comment,用于标注
$ ssh-keygen -C "shixi@localhost"

```

##### ssh-keyscan

##### ssh-agent

##### keychain (第三方工具)

OSX系统提供了一个非常棒的图形界面的密码管理工具叫Keychain，可以方便地存储各种密钥、证书、密码，并能缓存密钥的passphrase，只需要第一次使用的时候输入，以后会自动从内存读取，也就是免密码登录

另外一个keychain是一个命令行脚本，也可以将密码缓存，达到免密码认证的目的

### ~/.ssh/config 配置文件

### 免密码认证配置

* Step 1: 使用ssh-keygen生成密钥对
* Step 2: 使用

### SSH 错误 Debug

 * 检查文件权限是否正确

| 文件 | 权限 |
|-----|-----|
| ~/.ssh| 700 |
| ~/.ssh/authorized_keys| 600 |
| ~/.ssh/authorized_keys2| 600 |
| ~/.ssh/id_rsa | 600 |
| ~/.ssh/id_rsa.pub| 600 |
| ~/.ssh/config| 600 |

* 使用ssh -v 参数来打印debug信息

```bash

# 1个-v打印debug1
$ ssh -v xhost 
OpenSSH_6.9p1, LibreSSL 2.1.7
debug1: Reading configuration data /Users/Gao/.ssh/config
debug1: /Users/Gao/.ssh/config line 2: Applying options for xhost
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: /etc/ssh/ssh_config line 102: Applying options for *
......


# 2个 -vv 打印debug2
$ ssh -vv xhost 
OpenSSH_6.9p1, LibreSSL 2.1.7
debug1: Reading configuration data /Users/Gao/.ssh/config
debug1: /Users/Gao/.ssh/config line 2: Applying options for xhost
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: /etc/ssh/ssh_config line 102: Applying options for *
debug2: ssh_connect: needpriv 0
......

# 3个 -vvv 打印debug3

$ ssh -vvv xhost 
OpenSSH_6.9p1, LibreSSL 2.1.7
debug1: Reading configuration data /Users/Gao/.ssh/config
debug1: Remote protocol version 2.0, remote software version OpenSSH_4.3
debug2: fd 3 setting O_NONBLOCK
debug3: hostkeys_foreach: reading file "/Users/Gao/.ssh/known_hosts"
......


```


### SSH Dynamic Tunnelling(动态端口转发)——架设一个基于SSH的加密socket代理


这个有什么用？答：最实用的就是利用SSH加密隧道穿越那堵伟大的墙~~

原理就是，在本地监听一个端口，将数据包发动到隧道中，再从隧道的另外一头(ssh server)发到网络上，我们需要有一个SSH服务器在那堵墙的外面，并且有一个合法的ssh账号，然后两步：

第一步： 建立隧道

```bash

#很简单，仅需要一个-D参数,指定本地要监听的端口号，myvps是SSH服务器，当然可以用IP或者它的公网域名
#当然你可能还得用-l指定用户名，-i指定密钥文件 -p指定端口.
local~$ ssh -D 22388 myvps

#回到local，查看是否监听
local~$ netstat -nlt | grep 22388

```

第二步： 配置你的web客户端的代理配置，比如浏览器，chrome或者firefox也有相关插件(switchsharp)可以做自动或者手动切换, 使用curl命令检查代理是否生效:

```bash

local~$ curl --socks5 127.0.0.1:22388 http://facebook.com

```

通常这种连接容易断，用 autossh 可以断掉重连,screen工具可以将其放到后端执行

```bash

$ autossh -D 22388 myvps

```

### SSH Static Tunnelling(静态端口转发)——本地mysql连接远程vps上的mysqld服务

我们的vps主机上的mysqld进程为了安全一般只监听127.0.0.1的3306端口，这样外网没法直接访问到，我们可以使用SSH的静态隧道技术，将本地一个端口比如33060映射到远程主机的3306端口，这样我们访问本地的33060端口，包会被转发到隧道对端主机的本地3306端口。

仅需要一个参数： -L 127.0.0.1:33060:127.0.0.1:3306, -L(Listening) 含义是在本地监听一个33060端口，映射到隧道对端的 127.0.0.1:3306端口。mylinode是我的vps主机的别名。

```bash

#建立隧道
local~ $ ssh -L 127.0.0.1:33060:127.0.0.1:3306 mylinode

#查看是否已经监听
local~ $ netstat -nlt | grep 33060

```

### SSH Reverse Tunnelling(反向隧道)——使用Xdebug调试远程主机上的PHP程序

Leave a reply
如果我们调试的PHP程序部署在远程的公网的主机上，而我们是办公网络在一个防火墙或者路由器后面，那么PHP将无法连接我们的调试客户端（当然，从TCP角度来说，我们是服务端，PHPserver是客户端），因此我们可以建立一个反向隧道，将本地的9000端口映射到服务器的127.0.0.1:9000.即，server上9000端口的包会转发到我们本机上。

仅需要一个 -R (Reverse) 参数

```bash

$ ssh -R 127.0.0.1:9000:127.0.0.1:9000 user@your-remote-app-server.com

```
