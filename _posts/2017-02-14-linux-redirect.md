---
date: '2017-02-14 13:23 +0100'
published: true
title: 'Linux - Redirect '
category:
  - Linux
---
Redirect port 80 to port 3000 with this command:

```
sudo iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j REDIRECT --to-port 3000
```

*You should also edit your /etc/rc.local file and add that line minus the sudo. That will add the redirect when the machine boots up. You don't need sudo in /etc/rc.local because the commands there are run as root when the system boots.*
