---
layout: posts
title:  "command git usage"
date:   2015-07-01 00:00:13
categories: git cmd
permalink: /tech/command-git-usage
---



# git use client certificate

```bash

## convert p12 format to pem
$ openssl pkcs12 -in cert.p12 -out cert.pem -nodes
Enter Import Password:     # Enter your password here
MAC verified OK


## git config
$ git config http.sslCert ~/path/to/ssl/cert.pem

## test
$ git

```
