---
date: '2016-11-05 17:51 +0100'
published: false
title: React - Gatsby blog
category:
  - react
---

Denna artikeln går igenom hur vi skapar en blog site med Gatsby. 

För att komma igång snabbare använder vi ett starter theme:

```bash
$ gatsby new quickstart https://github.com/gatsbyjs/gatsby-starter-blog
cd quickstart
```

I Gatsby har varje artikel har sin egen mapp och inuti den katalogen är en fil, index.md. Dessa hitar du i mappen "pages".

Skapa en mapp som heter test, och sedan skapa ett nytt textdokument i den mappen (index.md). Precis som i t.ex. Jekyll använder man front-matter i Gatsby. 

```javascript
---
styleoverrides: '/blog.css'
title: testing quickblog
date: "2016-12-14T22:22"
layout: post
readNext: "/hi-folkes/"
path: "test"
---

Här skriver du markdown som blir själva innehållet i din post. 

```



