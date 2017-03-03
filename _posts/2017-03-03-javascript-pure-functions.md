---
date: '2017-03-03 21:04 +0100'
published: true
title: JavaScript - Pure functions
category:
  - JavaScript
---
A pure functions always give the same output on a specific input, they are not allowed to update object values inside the funciton and no asynchronous calls.

**Pure**

```js
function add(a,b)
{
    return a + b
}
```

**Not Pure**

```js
// 1) The same output with the same input
var a = 3
function add(b)
{
    return a + b
}
// 2) Side effects
var result
function add (a,b)
{
    result = a + b
    return result
}
// 3) Updates the values inside the function
function add (a,b)
{
	return a + b + new Date().getSeconds()
}
```

## Real world example 

This first example changes the value of the original object

```js
function changeProp(obj)
{

    // obj.name = 'Ilona'
    // return obj
}
var startingValue = {
    name: 'andree',
    age: 25
}
var res = changeProp(startingValue)
console.log(res)
console.log(startingValue)
```

This second example is pure, it does not update the original object

```js
function changeProp(obj)
{
    return {
        ...obj,
        name: 'Ilona'
    }
}
var startingValue = {
    name: 'andree',
    age: 25
}
var res = changeProp(startingValue)
console.log(res)
console.log(startingValue)
```