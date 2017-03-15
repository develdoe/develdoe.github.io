---
date: '2017-03-15 13:15 +0100'
published: true
title: JavaScript - String functions
category:
  - JavaScript
---
> JS treats primitive values as objects.  

## length

Returns the length property:

```js
var str = "adsf"
console.log(str.length) // => 4
```

---

## Search strings

### indexOf

Returns the index position of the first occurence of a specific text in a string:


```js
var str = "Please locate where 'locate' occurs"
console.log(str.indexOf("locate")) // => 7
```

### lastIndexOf

Returns the index position of the last occurence:

```js 
var str = "Please locate where 'locate' occurs"
console.log(str.lastIndexOf("locate")) // => 21
```

**Note: both indexOf methods return - 1 if the string is not found.**

> JavaScript counts positions from zero.
0 is the first position in a string, 1 is the second, 2 is the third ...

---

### search

Returns the position

```js
var str = "Please locate where 'locate' occurs"
console.log(str.search("locate") // => 7
```

**Note: indexOf and search() are equal, but search can take much more powerfull search values (like regular expressions).**

## includes(searchString)

determines whether one string may be found within another string, returning true or false

```js
var str = 'To be, or not to be, that is the question.';

console.log(str.includes('To be'));       // true
console.log(str.includes('question'));    // true
console.log(str.includes('nonexistent')); // false
console.log(str.includes('To be', 1));    // false
console.log(str.includes('TO BE'));       // false
```

#### Polyfill

```js
if (!String.prototype.includes) {
  String.prototype.includes = function(search, start) {
    'use strict';
    if (typeof start !== 'number') {
      start = 0;
    }
    
    if (start + search.length > this.length) {
      return false;
    } else {
      return this.indexOf(search, start) !== -1;
    }
  };
}
```

---

## Extracting

* slice(start, end)
* substring(start, end)
* substr(start, length)

### slice(start,end)

Returns a extracted part in a new string. 

```js
var str = "apple, banana, kiwi"
console.log(str.slice(7,13)) // => banana
```

If you omit the second param, the method will slice out the rest of the string:

```js
var str = "apple, banana, kiwi"
console.log(str.slice(7)) // => banana, kiwi
```

or the get the last 12 characters:

```js
var str = "apple, banana, kiwi"
var res = str.slice(-12) // => banana, kiwi
```

### substring(start, end)

The only differens is that substring cannot use negative indexes.

```js
var str = "apple, banana, kiwi"
console.log(str.slice(7,13)) // => banana
```

### substr(start, length)

Similar to slice, only last param dictates lenght of the extraction.

```js
var str = "apple, banana, kiwi"
console.log(str.slice(7,6)) // => banana
```


**Note: if the first start parameter is negative the position is counted from the end of the strin & if the second param is omited the rest is sliced out.**

```js
var str = "apple, banana, kiwi"
console.log(str.slice(-4)) // => kiwi
```

---

## Replacing

### repace()

Replaces with parameter value:

```js
var str = "apple, banana, kiwi"
console.log(str.replace("banana","rasberry")) // => apple, rasberry, kiwi
```

Can also take Regular Expressions.

```js
var str = "apple, banana, kiwi"
console.log(str.replace(/banana/g,"rasberry")) // => apple, rasberry, kiwi
```

**Note: replace returns a new string with the new value.**

---

## Converting

### toUpperCase()

Converts to upper case:

```js
var str = "case"
console.log(str.toUpperCase()) // => CASE
```

### toLowerCase()

Converts to lower case:

```js
var str = "CASE"
console.log(str.toLowerCase()) // => cas
```

----

## Merge strings

### concat(separator, str,...)

Joins two or more strings:

```js
var text1 = "Hello";
var text2 = "World";
console.log(text1.concat(" ", text2)) // => Hello World
```

**Note: concat returns a new string with the new value.**

---

## Extracing characters

### charAt(position)

Returns the character at a specified index:

```js
var str = "HELLO WORLD";
console.log(str.charAt(0)) // => H 
```

### charCodeAt()

Returns the unicode of the char at specified index:

```js
var str = "HELLO WORLD";
console.log(str.charAt(0)) // => 72
```

### codePointAt(pos) ES2015

returns a non-negative integar that is the Unicode point value

**If there is no element at the specified position, undefined is returned.**

```js
'ABC'.codePointAt(1);          // => 66
'\uD800\uDC00'.codePointAt(0); // => 65536
'XYZ'.codePointAt(42); // => undefined
```

#### Polyfill

```js
/*! http://mths.be/codepointat v0.1.0 by @mathias */
if (!String.prototype.codePointAt) {
  (function() {
    'use strict'; // needed to support `apply`/`call` with `undefined`/`null`
    var codePointAt = function(position) {
      if (this == null) {
        throw TypeError();
      }
      var string = String(this);
      var size = string.length;
      // `ToInteger`
      var index = position ? Number(position) : 0;
      if (index != index) { // better `isNaN`
        index = 0;
      }
      // Account for out-of-bounds indices:
      if (index < 0 || index >= size) {
        return undefined;
      }
      // Get the first code unit
      var first = string.charCodeAt(index);
      var second;
      if ( // check if it’s the start of a surrogate pair
        first >= 0xD800 && first <= 0xDBFF && // high surrogate
        size > index + 1 // there is a next code unit
      ) {
        second = string.charCodeAt(index + 1);
        if (second >= 0xDC00 && second <= 0xDFFF) { // low surrogate
          // http://mathiasbynens.be/notes/javascript-encoding#surrogate-formulae
          return (first - 0xD800) * 0x400 + second - 0xDC00 + 0x10000;
        }
      }
      return first;
    };
    if (Object.defineProperty) {
      Object.defineProperty(String.prototype, 'codePointAt', {
        'value': codePointAt,
        'configurable': true,
        'writable': true
      });
    } else {
      String.prototype.codePointAt = codePointAt;
    }
  }());
}
```

---

## Accessing as a array

**Accessing a string as an array is unpredictable and you should not do it!**

*If you want to access a string as an array, you should convert it first*

---

## Converting to array

### split(char)

```js
var txt = "a,b,c,d,e";   // String
txt.split(",");          // Split on commas
txt.split(" ");          // Split on spaces
txt.split("|");          // Split on pipe 
var str = "Hello";       // String
str.split("");           // Split in characters 
```

If separator is omitted, the returned array will contain the whole string at index 0

