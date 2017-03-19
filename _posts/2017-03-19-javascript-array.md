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

JavaScript arrays are zero-indexed: the first element of an array is at index 0, and the last element is at the index equal to the value of the array's length property minus 1.

```js
var arr = ['this is the first element', 'this is the second element'];
console.log(arr[0]);              // logs 'this is the first element'
console.log(arr[1]);              // logs 'this is the second element'
console.log(arr[arr.length - 1]); // logs 'this is the second element'
```

Array elements are object properties in the same way that toString is a property, but trying to access an element of an array as follows throws a syntax error, because the property name is not valid:

```js
console.log(arr.0); // a syntax error
```

There is nothing special about JavaScript arrays and the properties that cause this. 

JavaScript properties that begin with a digit cannot be referenced with dot notation; and must be accessed using bracket notation. For example, if you had an object with a property named '3d', it can only be referenced using bracket notation. E.g.:

```js 
var years = [1950, 1960, 1970, 1980, 1990, 2000, 2010];
console.log(years.0);   // a syntax error
console.log(years[0]);  // works properly
```

```js
renderer.3d.setTexture(model, 'character.png');     // a syntax error
renderer['3d'].setTexture(model, 'character.png');  // works properly
```

Note that in the 3d example, '3d' had to be quoted. It's possible to quote the JavaScript array indexes as well (e.g., years['2'] instead of years[2]), although it's not necessary. 

The 2 in years[2] is coerced into a string by the JavaScript engine through an implicit toString conversion. It is for this reason that '2' and '02' would refer to two different slots on the years object and the following example could be true:

```js
console.log(years['2'] != years['02']);
```

Similarly, object properties which happen to be reserved words(!) can only be accessed as string literals in bracket notation(but it can be accessed by dot notation in firefox 40.0a2 at least):

```js
var promise = {
  'var'  : 'text',
  'array': [1, 2, 3, 4]
};

console.log(promise['var']);
```

### Relationship between length and numerical properties

A JavaScript array's length property and numerical properties are connected.

Several of the built-in array methods (e.g., join, slice, indexOf, etc.) take into account the value of an array's length property when they're called.

Other methods (e.g., push, splice, etc.) also result in updates to an array's length property.

```js
var fruits = [];
fruits.push('banana', 'apple', 'peach');

console.log(fruits.length); // 3
```

When setting a property on a JavaScript array when the property is a valid array index and that index is outside the current bounds of the array, the engine will update the array's length property accordingly:

```js
fruits[5] = 'mango';
console.log(fruits[5]); // 'mango'
console.log(Object.keys(fruits));  // ['0', '1', '2', '5']
console.log(fruits.length); // 6
```

Increasing the length:

```js
fruits.length = 10;
console.log(Object.keys(fruits)); // ['0', '1', '2', '5']
console.log(fruits.length); // 10
```

Decreasing the length property does, however, delete elements.

```js
fruits.length = 2;
console.log(Object.keys(fruits)); // ['0', '1']
console.log(fruits.length); // 2
```

More on [Array.length](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/length).

### Creating an array using the result of a match

The result of a match between a regular expression and a string can create a JavaScript array. 

This array has properties and elements which provide information about the match. Such an array is returned by RegExp.exec, String.match, and String.replace. 

To help explain these properties and elements, look at the following example and then refer to the table below:

```js
// Match one d followed by one or more b's followed by one d
// Remember matched b's and the following d
// Ignore case

var myRe = /d(b+)(d)/i;
var myArray = myRe.exec('cdbBdbsbz');
```

The properties and elements returned from this match are as follows:

|Property/Element|Description|Example|
|input|A read-only property that reflects the original string against which the regular expression was matched.|cdbBdbsbz|
|index|A read-only property that is the zero-based index of the match in the string.|1|
|[0]|A read-only element that specifies the last matched characters.|dbBd|
|[1], ...[n]|Read-only elements that specify the parenthesized substring matches, if included in the regular expression. The number of possible parenthesized substrings is unlimited.|[1]: bB[2]: d|

### Properties

#### Array.length

The Array constructor's length property whose value is 1.

#### get Array[@@species]

The constructor function that is used to create derived objects.

#### Array.prototype

Allows the addition of properties to all array objects.

### Methods

#### Array.from()

Creates a new Array instance from an array like or iterable object

#### Array.isArray()

Returns boolean

#### Array.of()

Creates a new Array instance with a variable number of arguments, regardless of number or type of the arguments.

## Array Instances

All Array instances inherits from Array.prototype. The prototype of the Array constructor can be modified to effect all Array instances. 

### Properties

#### Array.prototype.constructor

Specifies the function that creates an object's prototype.

#### Array.prototype.length

Reflects the number of elements in an array.

#### Array.prototype[@@unscopables] 

A symbol containing property names to exclude from a with binding scope.

### Methods

#### Mutator methods

These methods modify the array.

##### Array.prototype.copyWithin()

Copies a sequence of a array elements within the array.

###### Syntax

```
arr.copyWithin(target)
arr.copyWithin(target, start)
arr.copyWithin(target, start, end)
```

###### Example

```js
['alpha', 'bravo', 'charlie', 'delta'].copyWithin(2, 0);
// results in ["alpha", "bravo", "alpha", "bravo"]
```

##### Array.prototype.fill()

Fills all the elements of an array from a start index to an end index with a static value.

###### Syntax

```
arr.fill(value)
arr.fill(value, start)
arr.fill(value, start, end)
```

###### Example

```js
var numbers = [1, 2, 3]
numbers.fill(1);
// results in [1, 1, 1]
```

##### Array.prototype.pop()

Removes the last element from an array and returns that element.

###### Syntax

```
arr.pop()
```

###### Example

```js
var a = [1, 2, 3];
a.pop();
console.log(a); // [1, 2]
```

##### Array.prototype.push()

Adds one or more elements to the end of an array and returns the new length of the array.

###### Syntax

```
arr.push([element1[, ...[, elementN]]])
```

###### Example

```js
var sports = ['soccer', 'baseball'];
var total = sports.push('football', 'swimming');

console.log(sports); // ['soccer', 'baseball', 'football', 'swimming']
console.log(total);  // 4
```

##### Array.prototype.reverse()

Reverses the order of the elements of an array in place — the first becomes the last, and the last becomes the first..

###### Syntax

```
a.reverse()
```

###### Example

```js
var a = ['one', 'two', 'three'];
a.reverse(); 

console.log(a); // ['three', 'two', 'one']
```

##### Array.prototype.shift()

Removes the first element from an array and returns that element.

###### Syntax

```
arr.shift()
```

###### Example

```js
var a = [1, 2, 3];
var b = a.shift();

console.log(a); // [2, 3]
console.log(b); // 1
```