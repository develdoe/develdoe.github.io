---
date: '2017-02-14 17:35 +0100'
published: true
title: SSH - Lägg till din SSH nyckel till en Server
category:
  - Development
---
## How To Set Up SSH Keys

### Generate

```
ssh-keygen -t rsa
```
### show

```
cat .ssh/id_rsa.pub
```

Copy that content all the way from ssh-rsa..... to your email part.

### Add your key 

Logga in på din server och klistra in din shh nyckel (under om det finns befintliga i filen) `.ssh/authorized_keys` 
