---
date: '2016-09-30 10:30 +0200'
published: true
title: Jekyll Serve Command Options
category:
  - Jekyll
---
In addition to the options below, the serve sub-command can accept any of the options for the build sub-command, which are then applied to the site build which occurs right before your site is served.

|SETTING|OPTIONS AND FLAGS|
|:------|----------------:|
|**Local Server Port** Listen on the given port.| port: PORT --port PORT|
|**Local Server Hostname** Listen at the given hostname.|host: HOSTNAME --host HOSTNAME|
|**Base URL** Serve the website from the given base URL|baseurl: URL --baseurl URL|
|**Detach** Detach the server from the terminal|detach: BOOL -B, --detach|
|**Skips the initial site build** Skips the initial site build which occurs before the server is started.|--skip-initial-build|
|**X.509 (SSL) Private Key** SSL Private Key.|--ssl-key|
|**X.509 (SSL) Certificate** SSL Public certificate.|--ssl-cert|

**Do not use tabs in configuration files since, this will either lead to parsing errors, or Jekyll will revert to the default settings. Use spaces instead**
