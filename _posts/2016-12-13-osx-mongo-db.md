---
date: '2016-12-13 16:09 +0100'
published: true
title: OSX - Mongo DB
---
First you need to install [Homebrew](http://develdoe.com/2016/osx-homebrew/)

**Update brew**

```bash
brew update
```

**Install**

*Binaries*

```bash
brew install mongodb
```

*TLS/SSL*

```bash
brew install mongodb --with-openssl
```

**Data Dir**

```bash
mkdir -p /data/db
```

**Permissions**

```bash
sudo chown -R `id -u` /data/db
```

**Run**

```bash
mongod
```