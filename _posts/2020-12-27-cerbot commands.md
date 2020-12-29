---
title: "Cerbot"
date: 2020-12-27T20:00:00
categories:
  - blog
tags:
  - General
  - Certbot
  - Proxy
---

Check if new vhost would actually complete the challenge: 
```
certbot certonly --nginx --rsa-key-size 4096 -d pete.no --dry-run
```

Perform the actual query
```
certbot --nginx --rsa-key-size 4096 -d pete.no
```
