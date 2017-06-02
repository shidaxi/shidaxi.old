---
title: web服务不能访问了如何进行排错
date: '2016-01-09 10:01:18 -0800'
author: shidaxi
categories:
  - devops
permalink: /posts/web-service-troubleshooting-steps
---

devops作为服务的owner和maintainer，经常会遇到一些帮助请求，描述方式一般是：""某某服务挂了吗",  "某某服务怎么访问不了"，“浏览器一直在转没有反应”，“重连vpn也不好使”，“清理浏览器缓存也不好使”，“重启电脑也不好使”。。。等等， 这些都不是有效的的问题描述 和 解决问题的办法。有效的问题描述应该提供一些关键信息，例如：  URL、页面截图、域名、哪个应用、哪个环境、现象描述、浏览器展示的错误代码、浏览器开发者工具信息、错误日志，甚至可能需要你做一些初步的troubleshoot得到更多信息，提供给服务的管理员\(一般是devops\)，帮助devops理解在你所处的环境会有哪些问题，以及进一步解决问题。

如果遇到访问错误，自己无从下手，可以尝试按照下面的方法做一下排错，保留得到的结果，如果自己无法判断原因所在，将结果发给devops，一起排查。

1. 解析一下域名的DNS解析

   ```bash
   # Linux/OSX (确保安装 dns-utils 或者 bind-utils)
   $ dig www.dianrong.com -t A  

   # Linux/OSX/Windows
   $ nslookup www.dianrong.com
   ```

2. 查看一下电脑DNS配置

   ```bash
   # Windows 
   user    > ipconfig/all  

   # Linux/osx User
    $ cat /etc/resolv.conf 
    nameserver 10.18.17.11
    nameserver 10.18.17.12
   ```

3. 确认网络层是否联通\(路由是否可达\)

   ```bash

   # 【注意】ping仅供参考，如果对端不响应ICMP包，一样ping不通，但不一定路由不可达
   $ ping 10.18.19.28 
    $ ping www.dianrong.com

   ```

4. 确认传输层\(TCP层\)是否可达，即能建立连接

   ```bash

   # 结果包含 Connected to说明建连成功
   $ telnet www.dianrong.com 80
    Trying 219.153.73.205... 
   Connected to 5beb99f12df722a0.cdn.jiashule.com. 
   Escape character is '^]'.

   ```

5. 确认应用层\(HTTP层\)是否工作，即服务端能返回HTTP响应\(Response\)

   ```bash

   $ curl https://www.dianrong.com/api/v2/profile 

   # 加-v参数获取更详细的 SSL握手信息 和 http header信息 
   $ curl -v https://www.dianrong.com/api/v2/profile

   ```

1. 如果是技术人员，可以打开浏览器的debug工具条查看一下  

1. 如果还定位不到原因，将如上步骤获取的信息截图发送给devops来做排查



