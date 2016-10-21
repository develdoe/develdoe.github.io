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

## Usage

Install the latest release of node:

```bash
nvm install node
```

Use the installed version:

```bash
nvm use node
```

Or run it:

```bash
nvm run node --version
```

Or, you can run any arbitrary command in a subshell with the desired version of node:

```bash
nvm exec 4.2 node --version
```

Get the path to the executable to where it was installed:

```bash
nvm which 5.0
```

In place of a version pointer like "0.10" or "5.0" or "4.2.1", you can use the following special default aliases with nvm install, nvm use, nvm run, nvm exec, nvm which, etc:

* node: this installs the latest version of node
* iojs: this installs the latest version of io.js
* stable: this alias is deprecated, and only truly applies to node v0.12 and earlier. Currently, this is an alias for node.
* unstable: this alias points to node v0.11 - the last "unstable" node release, since post-1.0, all node versions are stable. (in semver, versions communicate breakage, not stability).

If you want to use the system-installed version of node, you can use the special default alias "system":

```bash
nvm use system
nvm run system --version
```

Listing versions:

```bash
nvm ls
```

If you want to see what versions are available to install:

```bash
nvm ls-remote
```

To restore your PATH, you can deactivate it:

```bash
nvm deactivate
```

To set a default Node version to be used in any new shell, use the alias 'default':

```bash
nvm alias default node
```

`nvm use` will not, by default, create a "current" symlink. Set $NVM_SYMLINK_CURRENT to "true" to enable this behavior, which is sometimes useful for IDEs. Note that using nvm in multiple shell tabs with this environment variable enabled can cause race conditions.
