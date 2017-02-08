---
date: '2017-02-06 15:29 +0100'
published: true
title: JavaScript - Shorter Arrow Functions & this keyword
category:
  - JavaScript
---
**Normal function**

```js 
var names = ["andree","ilona","benedetta"]

names.foreach(function (name) {
    console.log('foreach 'name)
})
```

**statements arrow function**

```js
var names = ["andree","ilona","benedetta"]

names.foreach((name) => {
    console.log('foreach 'name)
})
```

**expression arrow function**

```js
var names = ["andree","ilona","benedetta"]

names.foreach((name) =>  console.log('foreach 'name))
```

## More...


*This touches the concept of scopes and the use of this binging. You can read more about these subject at [JavaScript - this](/2017/javascript-this/) & [JavaScript - Using the bind method](/2017/javascript-using-the-bind-method/) articles.*

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