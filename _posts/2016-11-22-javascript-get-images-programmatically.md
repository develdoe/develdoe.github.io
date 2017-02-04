---
date: '2016-11-22 09:37 +0100'
published: true
title: JavaScript - Get images programmatically
category:
  - JavaScript
---
```js
var img = new Image();
img.onload = function() {
  alert(this.width + 'x' + this.height);
}
img.src = '{path to image}';
```
