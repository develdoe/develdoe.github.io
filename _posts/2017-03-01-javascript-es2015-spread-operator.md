---
date: '2017-03-01 02:45 +0100'
published: true
title: JavaScript - ES2015 Spread Operator
category:
  - JavaScript
---

**old style**

```js
function add(a,b) {
    return a + b
}

console.log(add(3,1))
```

**es2015**

```js
var toAdd = [9,5]

console.log(add(...toAdd))
```