---
date: '2017-11-09 14:51 +0100'
published: true
title: JavaScript - Array.forEach är långsamt
---
`Array.ForEach` är cirka 95% långsammare än en vanlig `for()`.

**använd inte:**
```js
arr.forEach(function (item) {
  someFn(item);
})
```

**använd**
```js
for (var i = 0, len = arr.length; i < len; i++) {
  someFn(arr[i]);
}
```
