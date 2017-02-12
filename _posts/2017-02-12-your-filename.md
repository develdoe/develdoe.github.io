---
date: '2017-02-12 18:47 +0100'
published: true
title: JavaScript - Get Locale
category:
  - JavaScript
---
```js
var getLang = function() {
        if (navigator.languages != undefined) return navigator.languages[0]
        else return navigator.language
}
```