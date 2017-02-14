---
date: '2017-02-14 14:39 +0100'
published: true
title: Git - Server
category:
  - Development
---
In order to do any collaboration in Git, you’ll need to have a remote Git repository. 

Although you can technically push changes to and pull changes from individuals' repositories, doing so is discouraged because you can fairly easily confuse what they’re working on if you’re not careful. 

Furthermore, you want your collaborators to be able to access the repository even if your computer is offline.

The preferred method for collaborating with someone is to set up an intermediate repository that you both have access to, and push to and pull from that.

Running a Git server is fairly straightforward. 

First, you choose which protocols you want your server to communicate with. 

A remote repository is generally a bare repository – a Git repository that has no working directory. 

Because the repository is only used as a collaboration point, there is no reason to have a snapshot checked out on disk; it’s just the Git data. 

In the simplest terms, a bare repository is the contents of your project’s .git directory and nothing else.

---

Git can use four major protocols to transfer data: Local, HTTP, Secure Shell (SSH) and Git. 

The most basic is the Local protocol, in which the remote repository is in another directory on disk. 

To clone a repository like this or to add one as a remote to an existing project, use the path to the repository as the URL. For example, to clone a local repository, you can run something like this:

```
git clone /srv/git/project.git
```

Or 

```
git clone file:///srv/git/project.git
```

A common transport protocol for Git when self-hosting is over SSH. This is because SSH access to servers is already set up in most places – and if it isn’t, it’s easy to do. SSH is also an authenticated network protocol; and because it’s ubiquitous, it’s generally easy to set up and use.

To clone a Git repository over SSH, you can specify ssh:// URL like this:

```
git clone ssh://user@server/project.git
```

Or you can use the shorter scp-like syntax for the SSH protocol:

```
git clone user@server:project.git
```



