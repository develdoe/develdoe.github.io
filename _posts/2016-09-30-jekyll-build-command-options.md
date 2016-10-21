---
date: '2016-09-30 10:29 +0200'
published: true
title: Jekyll Build Command Options
category:
  - jek
---
The table below lists the available settings for Jekyll, and the various options (specified in the configuration file) and flags (specified on the command-line) that control them.

|SETTING|OPTIONS AND FLAGS|
|:---------|------:|
|**Regeneration** Enable auto-regeneration of the site when files are modified.|-w, --[no-]watch|
|**Configuration** Specify config files instead of using _config.yml automatically. Settings in later files override settings in earlier files.|--config FILE1[,FILE2,...]|
|**Drafts** Process and render draft posts.|show_drafts: BOOL --drafts|
|**Environment** Use a specific environment value in the build.|JEKYLL_ENV=production|
|**Future** Publish posts or collection documents with a future date.|future: BOOL --future|
|**LSI** Produce an index for related posts.|lsi: BOOL --lsi|
|**Limit Posts** Limit the number of posts to parse and publish.|limit_posts: NUM --limit_posts NUM|
|**Force polling** Force watch to use polling.|--force_polling|
|**Verbose output** Print verbose output.|-V, --verbose|
|**Silence Output** Silence the normal output from Jekyll during a build|-q, --quiet|
|**Incremental build** Enable the experimental incremental build feature. Incremental build only re-builds posts and pages that have changed, resulting in significant performance improvements for large sites, but may also break site generation in certain cases.|incremental: BOOL -I, --incremental|
|**Liquid profiler** Generate a Liquid rendering profile to help you identify performance bottlenecks.|profile: BOOL --profile|
