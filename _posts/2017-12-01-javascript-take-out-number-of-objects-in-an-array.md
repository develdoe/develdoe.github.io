---
date: '2017-12-01 11:57 +0100'
published: true
title: JavaScript - Take out number of objects in an array
---
```js
const LOAD_NUM = 10

var total // array with a number of items
var pickout // array with items we removed

function getItems() {
  if(pickout.length < total.length) {
      var append = total.slice(pickout.length, pickout.length + LOAD_NUM)
      pickout = pickout.concat(append)
  }
}
```
