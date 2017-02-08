---
date: '2017-02-06 15:29 +0100'
published: true
title: JavaScript - Shorter Arrow Functions & this keyword
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

There is a differance in bindings with normal and arrow functions. Arrow functions take on thier parents "this" binding.

Take a look at the code bellow:

```js
var person = {
    name: 'Andree',
    greet: function(){
        names.foreach(function(name){
            console.log(this.name + ' says hit to ' + name)
        })
    }
}
```

You might think that "this" keyword refers to the "person" object, however, it does not. You can fix this with anonymous functions:

```js
var person = {
    name: 'Andree',
    greet: function(){
        names.foreach((name)=>{
            console.log(this.name + ' says hit to ' + name)
        })
    }
}
```

This workes becouse Arrow functions does not update the "this" keyword. 

For a more indept explenation to "this" and its binding check out [JavaScript - this](/2017/javascript-this/) & [JavaScript - Using the bind method](/2017/javascript-using-the-bind-method/)
