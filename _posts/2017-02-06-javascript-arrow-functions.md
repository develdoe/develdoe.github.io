---
date: '2017-02-06 15:29 +0100'
published: true
title: JavaScript - Shorter Arrow Functions
category:
  - JavaScript
---
```js
var names = ["andree","ilona","benedetta"]

names.foreach(function (name) {
    console.log('foreach 'name)
})

names.foreach( (name) => {
    console.log('arrow func foreach ', name)
})

name.foreach( (name) => console.log(name) )
```

*Note that there is a differance in bindings with normal and arrow functions. Arrow functions take on thier parents "this" binding.*