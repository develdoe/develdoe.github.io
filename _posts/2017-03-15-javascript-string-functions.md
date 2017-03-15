---
date: '2017-03-15 13:15 +0100'
published: true
title: JavaScript - String functions
category:
  - JavaScript
---
> In JS, strings also have methods and properties besause JS treats primitive values as objects.  

## length

Returns the length property:

```js
var str = "adsf"
console.log(str.length) // => 4
```

---

## indexOf

Returns the index position of the first occurence of a specific text in a string:


```js
var str = "Please locate where 'locate' occurs"
console.log(str.indexOf("locate")) // => 7
```

## lastIndexOf

Returns the index position of the last occurence:

```js 
var str = "Please locate where 'locate' occurs"
console.log(str.lastIndexOf("locate")) // => 21
```

**Note: both indexOf methods return - 1 if the string is not found.**

> JavaScript counts positions from zero.
0 is the first position in a string, 1 is the second, 2 is the third ...

---

## search

Returns the position

```js
var str = "Please locate where 'locate' occurs"
console.log(str.search("locate") // => 7
```

**Note: indexOf and search() are equal, but search can take much more powerfull search values (like regular expressions).**

---

