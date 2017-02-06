---
date: '2017-02-06 15:29 +0100'
published: true
title: JavaScript - Arrow Functions
category:
  - JavaScript
---
> An arrow function expression has a shorter syntax than a function expression and does not bind its own this, arguments, super, or new.target. Arrow functions are always anonymous. These function expressions are best suited for non-method functions, and they cannot be used as constructors. (MDN, 2017)

*There is a differance in bindings with normal and arrow functions. Arrow functions take on thier parents "this" binding.*

**Basic**

```js
(param1, param2, …, paramN) => { statements }
(param1, param2, …, paramN) => expression
// equivalent to: (param1, param2, …, paramN) => { return expression; }

// Parentheses are optional when there's only one parameter:
(singleParam) => { statements }
singleParam => { statements }

// A function with no parameters requires parentheses:
() => { statements }
() => expression // equivalent to: () => { return expression; }
```

**Advanced**

```js
// Parenthesize the body to return an object literal expression:
params => ({foo: bar})

// Rest parameters and default parameters are supported
(param1, param2, ...rest) => { statements }
(param1 = defaultValue1, param2, …, paramN = defaultValueN) => { statements }

// Destructuring within the parameter list is also supported
var f = ([a, b] = [1, 2], {x: c} = {x: a + b}) => a + b + c;
f();  // 6
```

**Shorter functions**

```js
var names = ["andree","ilona","benedetta"]

names.foreach(function (name) {
    console.log('foreach 'name)
})

names.foreach( (name) => {
    console.log('arrow func foreach ', name)
})

name.foreach( (name) => console.log(name) )

var returnMe( (name) => name + "!")
console.log( returnMe("andree") ) 

```