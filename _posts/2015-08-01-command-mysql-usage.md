---
layout: posts
title:  "command mysql usage"
date:   2015-08-01 08:00
categories: mysql cmd
permalink: /tech/command-mysql-usage
---


Create a user

```mysql

mysql> CREATE USER 'admin'@'%' IDENTIFIED BY 'password';
Query OK, 0 rows affected (0.00 sec)

```

Grant privileges

```mysql

mysql> GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%';
Query OK, 0 rows affected (0.00 sec)

mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)

```
