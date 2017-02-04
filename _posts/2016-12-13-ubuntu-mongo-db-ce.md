---
date: '2016-12-13 14:49 +0100'
published: true
title: 'Ubuntu - Mongo DB CE '
category:
  - Linux
---
**key**

```bash
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
```

**Create a list file**

*Ubuntu 12.04*

```bash
echo "deb http://repo.mongodb.org/apt/ubuntu precise/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
```

*Ubuntu 14.04*

```bash
echo "deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
```

*Ubuntu 16.04*

```bash
echo "deb http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
```

**Reload local packages**

```bash
sudo apt-get update
```

**Install**

```bash
sudo apt-get install -y mongodb-org
```

**Start, Stop, Restart**

```bash
sudo service mongod start
sudo service mongod stop
sudo service mongod restart
```

**Verify**

```bash
vim /var/log/mongodb/mongod.log
```
