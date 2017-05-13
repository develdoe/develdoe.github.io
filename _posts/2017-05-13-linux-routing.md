---
date: '2017-05-13 05:00 +0200'
published: true
title: Linux - Routing
category:
  - Linux
---
#### Enable ip forwarding at the kernal lv

```
sysctl -w net.ipv4.ip_forward=1
```

#### Setup Nat (Network Address Translations)

```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
iptables -t filter -D FORWARD -j REJECT --reject-with icmp-host-prohibited
```