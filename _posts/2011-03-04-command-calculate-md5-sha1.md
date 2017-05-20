---
layout: posts
title:  "command to calculate md5 and sha1"
date:   2011-02-15 08:00
categories: cmd
permalink: /tech/command-calculate-md5-sha1
---


####计算字符串的md5哈希值

```bash

# md5sum命令，-n 避免混入回车符
$echo -n aaa | md5sum
47bce5c74f589f4867dbd57e9ca9f808

# php
$echo -n aaa | php -r 'echo(md5(stream_get_contents(STDIN))."\n");'
47bce5c74f589f4867dbd57e9ca9f808

#python 
$echo -n aaa | python26 -c "import sys,hashlib;print(hashlib.md5(sys.stdin.read()).hexdigest())"
47bce5c74f589f4867dbd57e9ca9f808

```

####计算文件的md5哈希

```bash

$cat readme.txt | md5sum
1718f90558dc25109894f98e2786fee1  -

$cat readme.txt | php -r 'echo(md5(stream_get_contents(STDIN))."\n");'
1718f90558dc25109894f98e2786fee1

$cat readme.txt | python26 -c "import sys,hashlib;print(hashlib.md5(sys.stdin.read()).hexdigest())"
1718f90558dc25109894f98e2786fee1

```

####计算文件的sha1

```bash

$cat readme.txt | sha1sum
d219146ba0bf3836a5f3e491242609bb735a3532  -

$cat readme.txt | php -r 'echo(sha1(stream_get_contents(STDIN))."\n");'
d219146ba0bf3836a5f3e491242609bb735a3532

$cat readme.txt | python26 -c "import sys,hashlib;print(hashlib.sha1(sys.stdin.read()).hexdigest())" 
d219146ba0bf3836a5f3e491242609bb735a3532

```
