---
date: '2016-10-15 14:52 +0200'
published: true
title: Node.js - NPM
category:
  - node
---
npm is the default package manager for the JavaScript runtime environment Node.js and it comes bundle with Node.js.

It provides two main functionalities:

* Online repositories for node.js packages/modules
* Command line utility to install Node.js packages, do version management and dependency management of Node.js packages.

NPM installs any dependency in node_modules directory lying in the folder where you execute the command. Locally deployed packages are accessible via require() method. For example, when we installed a module, it createa node_modules directory in the current directory where it installed the  module.

Globally installed packages/dependencies are stored in system directory. Such dependencies can be used in CLI (Command Line Interface) function of any node.js but cannot be imported using require() in Node application directly.

Verify version:

```bash
$ npm --version
```

Update:
```bash
$ sudo npm install npm -g
```

Installing Modules:

```bash
$ npm install <Module Name>
```

Uninstall:

```bash
$ npm uninstall module
```

Update:

```bash
$ npm update module
```

List all locally installed modules:

```bash
$ npm ls
```

List all globally installed modules:

```bash
$ npm ls -g
```

Search:

```bash
$ npm search module
```

## package.json

The best way to manage locally installed npm packages is to create a package.json file. These files is automatic created and populated when you install packages. However you can also manually re-configure them. 

A package.json file affords you a lot of great things:

* It serves as documentation for what packages your project depends on.
* It allows you to specify the versions of a package that your project can use using semantic versioning rules.
* Makes your build reproducable which means that its way easier to share with other developers.

As a bare minimum, a package.json must have:

* **"name"**: all lowercase, one word, no spaces, dashes and underscores allowed
* **"version"**: in the form of x.x.x
* follows (semver spec)[https://docs.npmjs.com/getting-started/semantic-versioning]


```javascript
{
  "name": "my-awesome-package",
  "version": "1.0.0"
}
```

### Create a Module

Creating a module requires package.json to be generated. 

```bash
$ npm init
```

You will need to provide all the required information about your module. You can take help from the above-mentioned package.json file to understand the meanings of various information demanded. Once package.json is generated, use the following command to register yourself with NPM repository site using a valid email address:

```bash
$ npm adduser
```

Publish:

```bash
npm publish
```
