---
date: '2016-09-28 14:24 +0200'
published: true
title: Jekyll Basic Usage
category:
  - jek
---
The Jekyll gem makes a jekyll executable available to you in your Terminal window. You can use this command in a number of ways:

```bash
$ jekyll build
# => The current folder will be generated into ./_site

$ jekyll build --destination <destination>
# => The current folder will be generated into <destination>

$ jekyll build --source <source> --destination <destination>
# => The <source> folder will be generated into <destination>

$ jekyll build --watch
# => The current folder will be generated into ./_site,
#    watched for changes, and regenerated automatically.
```
*The _config.yml master configuration file contains global configurations and variable definitions that are read once at execution time. Changes made to _config.yml during automatic regeneration are not loaded until the next execution.
Note Data Files are included and reloaded during automatic regeneration.*

**The contents of <destination> are automatically cleaned, by default, when the site is built. Files or folders that are not created by your site will be removed. Files and folders you wish to retain in <destination> may be specified within the <keep_files> configuration directive.
Do not use an important location for <destination>; instead, use it as a staging area and copy files from there to your web server.**

Jekyll also comes with a built-in development server that will allow you to preview what the generated site will look like in your browser locally.

```bash
$ jekyll serve
# => A development server will run at http://localhost:4000/
# Auto-regeneration: enabled. Use `--no-watch` to disable.

$ jekyll serve --detach
# => Same as `jekyll serve` but will detach from the current terminal.
#    If you need to kill the server, you can `kill -9 1234` where "1234" is the PID.
#    If you cannot find the PID, then do, `ps aux | grep jekyll` and kill the instance. [Read more](http://unixhelp.ed.ac.uk/shell/jobz5.html).
```

*As of version 2.4, the serve command will watch for changes automatically. To disable this, you can use jekyll serve --no-watch, which preserves the old behavior.*

```bash
$ jekyll serve --no-watch
# => Same as `jekyll serve` but will not watch for changes.
```

These are just a few of the available [configuration options](https://jekyllrb.com/docs/configuration/). Many configuration options can either be specified as flags on the command line, or alternatively (and more commonly) they can be specified in a _config.yml file at the root of the source directory. Jekyll will automatically use the options from this file when run. For example, if you place the following lines in your _config.yml file:

```bash
source:      _source
destination: _deploy
```

Then the following two commands will be equivalent:

```bash
$ jekyll build
$ jekyll build --source _source --destination _deploy
```

For more about the possible [configuration options](https://jekyllrb.com/docs/configuration/), see the configuration page.

If you’re interested in browsing these docs on-the-go, install the jekyll-docs gem and run jekyll docs in your terminal.
