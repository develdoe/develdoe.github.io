---
date: '2016-10-14 16:34 +0200'
published: true
title: 'Node Version Manager '
category:
  - node
---

## Installation

First you'll need to make sure your system has a c++ compiler. For OS X, Xcode will work, for Ubuntu, the build-essential and libssl-dev packages work.

On OS X, if you have/had a "system" node installed and want to install modules globally, keep in mind that:

* When using nvm you do not need sudo to globally install a module with npm -g, so instead of doing `sudo npm install -g grunt`, do instead `npm install -g grunt`
* If you have an ~/.npmrc file, make sure it does not contain any prefix settings.
* Do not keep your previous "system" node install. This might cause version mismatches.

To install or update nvm:

```bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.32.0/install.sh | bash
```

The script clones the nvm repository to ~/.nvm and adds the source line to your profile (~/.bash_profile, ~/.zshrc, ~/.profile, or ~/.bashrc).

Note: On OS X, if you get `nvm: command not found` after running the install script, your system may not have a [.bash_profile file] where the command is set up. Simply create one with `touch ~/.bash_profile` and run the install script again.

To verify that nvm has been installed, do:

```bash
command -v nvm
```
