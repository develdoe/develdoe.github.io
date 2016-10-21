---
date: '2016-09-28 13:38 +0200'
published: true
title: Jekyll Quick Start Guide
category:
  - jek
---
Here's how to get a boilerplate Jekyll site up and runnin

```bash
~ $ gem install jekyll bundler
~ $ jekyll new myblog
~ $ cd myblog
bundle install
bundle exec jekyll serve
```
Now browse to http://localhost:4000

*If you wish to install jekyll into an existing directory, you can do so by running jekyll new . from within the directory instead of creating a new one. If the existing directory isn’t empty, you’ll also have to pass the --force option like so jekyll new . --force.*


