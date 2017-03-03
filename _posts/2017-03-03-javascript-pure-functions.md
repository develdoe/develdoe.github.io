---
date: '2017-03-03 21:04 +0100'
published: true
title: JavaScript - Pure functions
category:
  - JavaScript
---
A pure functions always give the same output on a specific input and they are not allowed to update the values inside the funciton.

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
// updates the values inside the function
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