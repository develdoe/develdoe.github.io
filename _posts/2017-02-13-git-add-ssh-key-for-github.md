---
date: '2017-02-13 16:46 +0100'
published: true
title: Git - Add SSH key for Github
category:
  - Development
---
**Open terminal and type:**

```
ssh-keygen -t rsa -b 4096 -C "{youremail}"
```

**add the new key (use your filename):

```
ssh-add ~/.ssh/id_rsa
more ~/.ssh/id_rsa.pub
```

**copy the content prited by the `more`command:**

```
more ~/.ssh/id_rsa.pub
```

**Go to settings at [github](https://github.com/settings/keys) and past the content.**

**Go back to your terminal shell and authenticate**

```
ssh -T git@github.com
```
