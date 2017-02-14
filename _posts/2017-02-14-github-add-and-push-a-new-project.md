---
date: '2017-02-14 13:53 +0100'
published: true
title: Github - add and push a new project
category:
  - Development
---
Go to Github and add a new project.

**Init**

```
git init
```

**Add**

```
git add .
```

**commit**

```
git commit -m 'init'
```

**remote**

```
git remote add github {urltoyourproject}
```

**push**

```
git push -u github master
```

---

Now you can push simply by: 

```
git push github
```

and if you dont add new files, you can commit and add in one command.

```
git commit -a -m 'comment'
```

You can check remotes:

```
git remote -v
```

and git branch

```
git branch
```