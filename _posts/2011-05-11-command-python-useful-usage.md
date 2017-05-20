---
layout: posts
title:  "command python userful usage"
date:   2011-05-11 08:00
categories: python cmd
permalink: /tech/command-python-useful-usage
---

有时候我们需要从生产网上下载一个文件到办公网的电脑上，不能为了这一个小需求就去改webserver的配置吧，可以临时用一句python命令启动一个httpserver用于临时的下载，下载完成后Ctrl-C即可终止，比scp，rsync神马简单多了。

```bash

#先进入到你要访问的路径，当前路径会成为http server的根路径
$cd /home/shixi.gs/
$python -m SimpleHTTPServer
Serving HTTP on 0.0.0.0 port 8000 ...

```
