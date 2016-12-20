---
date: '2016-12-20 02:57 +0100'
published: true
title: JavaScript - Creating custom events
category:
  - js
---
```js
var event = new Event('build');

// Listen for the event.
elem.addEventListener('build', function (e) { ... }, false);

// Dispatch the event.
elem.dispatchEvent(event);
```
