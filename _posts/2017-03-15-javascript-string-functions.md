---
date: '2017-03-15 13:15 +0100'
published: true
title: JavaScript - String functions
category:
  - JavaScript
---
> In JS, strings also have methods and properties besause JS treats primitive values as objects.  

## length

Returns the length property:

```js
var str = "adsf"
console.log(str.length) // => 4
```

---

## Search for index

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

### concat(a,b,...)

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

