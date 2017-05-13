---
date: '2017-05-13 05:00 +0200'
published: true
title: CentOS - Router
category:
  - Linux
---
## Nics

#### /etc/sysconfig/network-scripts/ifcfg-eth0

```
...
NAME=WAN
ONBOOT=yes
...
```

#### /etc/sysconfig/network-scripts/ifcfg-eth1

```
...
IPADDR=192.168.111.1
NETMASK=255.255.255.0
NAME=LAN
ONBOOT=yes
...
```

## Routing

#### Enable ip forwarding at the kernal lv

```
sysctl -w net.ipv4.ip_forward=1
```

#### Setup Nat (Network Address Translations)

```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
iptables -t filter -D FORWARD -j REJECT --reject-with icmp-host-prohibited
iptables-save > /etc/sysconfig/iptables
```

## DHCP

#### Install

```
yum install dhcp -y
```

#### /etc/dhcp/dhcpd.conf

```
subnet 192.168.111.0 netmask 255.255.255.0 {
	range 192.168.111.166 192.168.111.170;
    option domain-name-servers 208.67.220.220;
    option domain-name "ODIN";
    option routers 192.168.111.1;
    option broadcast-address 192.168.111.255;
    default-lease-time 600;
    max-lease-time 7200;
}
```

#### Service start

```
chkconfig dhcpd on
service dhcpd restart
```
