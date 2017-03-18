---
date: '2017-03-19 00:16 +0100'
published: true
title: JavaScript - Array
category:
  - JavaScript
---
> An array is a systematic arrangement of similar objects, usually in rows and columns.

The JavaScript Array object is a global object that is used in the construction of arrays; which are high-level, list-like objects.

**Create an Array**

```js
var fruits = ['Apple', 'Banana']
console.log(fruits.length) // 2
```

**Access (index into) an Array item**

```js
var first = fruits[0] // Apple
var last = fruits[fruits.lenth - 1] // Banana
```

**Loop over an Array**

```js
fruits.forEach(function(item,index,array){
	console.log(item,index)
})
// Apple 0
// Banana 1
```

**Add to the end of an Array**

```js
var newLength = fruits.push('Orange')
// fruits = ['Apple','Banana','Orange']
// newLength = 3
```

**Remove from the end of an array**

```js
var last = fruits.pop() // Removes Orange
// fruits =  ['Apple','Banana']
// last = 'Orange'
```

**Remove from the front of the Array**

```js
var first = fruits.shift() // Remove Apple
// fruits = ["Banana"]
// first = "Apple"
```

**Find the index of an item in the Array**

```js
fruits.push('Mango')
// ['Apple','Banana','Mango']
var pos = fruits.indexOf('Banana') // 1
```

**Remove item by index**

```js 
var removed = fruits.splice(pos,1) // this is how to remove an item
// fruits = ['Apple','Mango']
```

**Remove items from index**

```js
var vegetables = ['Cabbage', 'Turnip', 'Radish', 'Carrot'];
console.log(vegetables); 
// ["Cabbage", "Turnip", "Radish", "Carrot"]

var pos = 1, n = 2;

var removedItems = vegetables.splice(pos, n); 
// this is how to remove items, n defines the number of items to be removed,
// from that position(pos) onward to the end of array.

console.log(vegetables); 
// ["Cabbage", "Carrot"] (the original array is changed)

console.log(removedItems); 
// ["Turnip", "Radish"]
```

**Copy an Array**

```js
var shallowCopy = fruits.slice() 
````

## Syntax

> [element0, element1, ..., elementN]
> new Array(element0, element1[, ...[, elementN]])
> new Array(arrayLength)