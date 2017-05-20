---
title: '使用bind-util工具集调试DNS问题'
date: 2016-01-20 14:08:18 -0800
author: shidaxi
categories: [command]
---

bind-util工具集包含多个dns查询命令，主要有以下几个

#### dig 命令

```bash

$ dig taobao.com

$ dig taobao.com +noall +answer  #只输出应答信息

$ dig -t NS taobao.com +noall +answer  #-t 指定记录类型，可以是 A NS MX TXT SOA 或者 any是所有

$ dig -x xx.xx.xx.xx # -x #反向解析

$ dig @8.8.8.8 taobao.com  #跳过本地dns，指定要查询的server


```

#### nslookup 命令

```bash

$ nslookup taobao.com

```
