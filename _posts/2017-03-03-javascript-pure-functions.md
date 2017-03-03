---
date: '2017-03-03 21:04 +0100'
published: true
title: JavaScript - Pure functions
category:
  - JavaScript
---
A pure function do not change outside of its function

**Pure**

```js
function add(a,b)
{
    return a + b
}
```

**Not Pure**

```js
var a = 3
function add(b)
{
    return a + b
}
// or
var result
function add (a,b)
{
    result = a + b
    return result
}
// also not pure
function add (a,b)
{
	return a + b + new Date().getSeconds()
}
```
