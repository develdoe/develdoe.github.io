---
date: '2017-02-14 17:35 +0100'
published: true
title: SSH - Lägg till din SSH nyckel till en Server
category:
  - Development
---
### Generera nyckel

```
ssh-keygen -t rsa
```
### visa nyckeln

```
cat .ssh/id_rsa.pub
```
Kopiera hela strängen från ssh-rsa... till din mail adress.

### klistra in din nyckel på servern

Logga in på din server och klistra in din shh nyckel (under om det finns befintliga i filen) `.ssh/authorized_keys` 
