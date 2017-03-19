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
```

## Syntax

```
[element0, element1, ..., elementN]
new Array(element0, element1[, ...[, elementN]])
new Array(arrayLength)
```

### Parameters 

**elementN**

A JavaScript array is initialized with the given elements. 

Except in the case where a single argument is passed to the Array constructor and that argument is a number (see the arrayLength parameter below). *Note that this special case only applies to JavaScript arrays created with the Array constructor, not array literals created with the bracket syntax.*

**arrayLength**

If the only argument passed to the Array constructor is an integer between 0 and 232-1 (inclusive), this returns a new JavaScript array with its length property set to that number. *Note: this implies an array of arrayLength empty slots, not slots with actual undefined values.* If the argument is any other number, a RangeError exception is thrown.

## Description

Arrays are list-like objects whose prototype has methods to perform traversal and mutation operations. 

Neither the length of a JavaScript array nor the types of its elements are fixed. 

Since an array's length can change at any time, and data can be stored at non-contiguous locations in the array, JavaScript arrays are not guaranteed to be dense; this depends on how the programmer chooses to use them. 

In general, these are convenient characteristics; but if these features are not desirable for your particular use, you might consider using typed arrays.

Some people think that you [shouldn't use an array as an associative array](www.andrewdupont.net/2006/05/18/javascript-associative-arrays-considered-harmful/). In any case, you can use plain objects instead, although doing so comes with its own caveats. See the post [Lightweight JavaScript dictionaries with arbitrary keys](http://www.less-broken.com/blog/2010/12/lightweight-javascript-dictionaries.html) as an example.

### Accessing array elements
