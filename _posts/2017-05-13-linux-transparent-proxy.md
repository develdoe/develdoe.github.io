---
date: '2017-05-13 05:29 +0200'
published: true
title: Linux - Transparent Proxy
category:
  - Linux
---
###### Install Squid 
```
yum install squid -y

```

###### /etc/squid/squid.conf
 
```
...
http_port 3128 intercept
...
```

##### Start Service

```
service squid restart
```

---

By now you have a proxy server, however, nothing is being blocked, lets go that now.

###### Routing

```
iptables -t nat -A PREROUTING -i eth1 -p tcp --dport80 -j DNAT --to 192.168.111.1:3128
```

###### Check access

```
cat /var/log/squid/access.log
```

The access log should have entries....