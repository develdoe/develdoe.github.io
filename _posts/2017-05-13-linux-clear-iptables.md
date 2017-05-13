---
date: '2017-05-13 08:30 +0200'
published: true
title: LINUX - Clear iptables
category:
  - Linux
---
```
iptables -F
iptables -X
iptables -t nat -F
iptables -t nat -X
iptables -t mangle -F
iptables -t mangle -X
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT
```
