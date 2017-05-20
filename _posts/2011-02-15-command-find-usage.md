---
layout: posts
title:  "command find usage"
date:   2011-02-15 08:00
categories: cmd
permalink: /tech/command-find-usage
---

find 基本用法

```bash

#find all files
$ find .

#find all directories
$ find . -type d

#find all files
$ find . -type f

#find with depth
$ find . -maxdepth 2 -mindepth 2 -type d

#find empty directories or empty files(size=0byte)
$ find . -empty -type d
$ find . -empty -type f

```


```bash

#按名字查找
find -name "*.bak"

# 7天之前的log
$ find . -name "*log*" -mtime +7

# 7天之内的log
$ find . -name "*log*" -mtime +7

#查找全局可写的文件
$ find . -perm 777

```

和xargs搭配使用，我们查找到符合条件的文件以后，需要进一步做操作，比如删除

```bash

#查找所有bak文件并且删除
$ find . -name "*.bak" | xargs rm

#查找所有7天前的日志并删除
$ find . -name "*log*" -mtime +7 | xargs rm {}

#处理包含空格的文件可能会使xargs处理异常，使用-print0参数处理
$ find . -type f -print0 | xargs -0 ls -lh

```
