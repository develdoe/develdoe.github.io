---
date: '2018-01-23 15:50 +0100'
published: true
title: 'SSH / överföringsfiler - Secure Copy, scp'
---
Precis som alla moderna Unix-liknande system har en SSH-klient, har de också SCP- och SFTP-klienter. För att kopiera en fil från din dator till en annan dator med ssh, gå till en kommandorad och skriv:

```
scp <file> <username>@<IP address or hostname>:<Destination>
```
