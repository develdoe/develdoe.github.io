---
date: '2016-09-30 10:31 +0200'
published: true
title: Jekyll Custom WEBrick Headers
category:
  - Jekyll
---
You can provide custom headers for your site by adding them to _config.yml

```bash
# File: _config.yml
webrick:
  headers:
    My-Header: My-Value
    My-Other-Header: My-Other-Value
```
