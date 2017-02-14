---
date: '2017-02-14 14:39 +0100'
published: true
title: Linux - Setting up a Git Server
category:
  - Development
---
First, you create a git user and a .ssh directory for that user:
 
```
$ sudo adduser git
$ su git
$ cd
$ mkdir .ssh && chmod 700 .ssh
$ touch .ssh/authorized_keys && chmod 600 .ssh/authorized_keys
```

Copy the ouput of your ssh key:

```
$ cat /tmp/id_rsa.john.pub
```

to the git user’s authorized_keys:

```
$ vim ~/.ssh/authorized_keys
```

Now, you can set up an empty repository for them on the server by running git init with the --bare option, which initializes the repository without a working directory:

```
$ cd /srv/git
$ mkdir project.git
$ cd project.git
$ git init --bare --shared
Initialized empty Git repository in /srv/git/project.git/
```

**(OPTIONAL)**
Add your key to ssh server conf file.

Edit:

```
$ vim ~/.ssh/config
```

Paste:

```
host example.com
 HostName example.com
 IdentityFile ~/.ssh/key
 User git
```


Then, you can push the first version of their project into that repository by adding it as a remote and pushing up a branch.  

Let’s use gitserver as the hostname of the server on which you’ve set up your git user and repository. If you’re running it internally, and you set up DNS for gitserver to point to that server, then you can use the commands pretty much as is (assuming that myproject is an existing project with files in it):

```
$ cd myproject
$ git init
$ git add .
$ git commit -m 'initial commit'
$ git remote add origin git@gitserver:/srv/git/project.git
$ git push -u origin master
```
