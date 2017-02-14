---
date: '2017-02-14 17:35 +0100'
published: true
title: SSH - add key to use at specific server
category:
  - Development
---
**~/.ssh/config**

```
host github.com
 HostName github.com
 IdentityFile ~/.ssh/id_rsa_github
 User git
```