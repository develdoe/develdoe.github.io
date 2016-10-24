---
date: '2016-10-24 13:37 +0200'
published: true
title: Javascript - Numbers
category:
  - js
socialImg: js.png
---
Unlike many languages, JS does not make an distinction between integer values and floating point values. 

When a number appear directly in a JS program, it is called a numeric literal. JS supports numeric literals in several formats. Numeric literal can be proceded by a minus sign to make it negative. Technically, however - is the unary negation operator and is not part of the numeric syntax.

All numbers are represented as floating-point values. JS represents numbers using the 64-bit floating-point format, the same format as "double" in many other languages. This format allows you to represent all integers between -9007199254740992(-2^53) and 9007199354740992(2^53), if you use integers bigger than this you might loos presicion in the trailing digits. 

Watch out for things like:

```javascript
0.1 + 0.2 == 0.30000000000000004
```

In practice, integer values are treated as 32-bit ints, and some implementations even store it that way until they are asked to perform an instruction that's valid on a Number but not on a 32-bit integer. 

This can be important for bit-wise operations and array indexing, since thay are performed with 32-bit integers.

The standard arithmetic operators are supported, including addition, subtraction, modulus (or remainder) arithmetic, and so forth. There's also a built-in object called Math that provides advanced mathematical functions and constants:

```javascript
Math.sin(3.5);
var circumference = Math.PI * (r + r);
```

You can convert a string to an integer using the built-in parseInt() function. This takes the base for the conversion as an optional second argument, which you should always provide:

```javascript
parseInt("123", 10); // 123
parseInt("010", 10); // 10
```

In older browsers, strings beginning with a "0" are assumed to be in octal (radix 8), but this hasn't been the case since 2013 or so. Unless you're certain of your string format, you can get surprising results on those older browsers:

```javascript
parseInt("010");  //  8
parseInt("0x10"); // 16
```

Here, we see the parseInt() function treat the first string as octal due to the leading 0, and the second string as hexadecimal due to the leading "0x". The hexadecimal notation is still in place; only octal has been removed.

If you want to convert a binary number to an integer, just change the base:

```javascript
parseInt("11", 2); // 3
```

Similarly, you can parse floating point numbers using the built-in parseFloat() function. Unlike its parseInt() cousin, parseFloat() always uses base 10.

You can also use the unary + operator to convert values to numbers:

```javascript
+ "42";   // 42
+ "010";  // 10
+ "0x10"; // 16
```

A special value called NaN (short for "Not a Number") is returned if the string is non-numeric:

```javascript
parseInt("hello", 10); // NaN
```

NaN is toxic: if you provide it as an input to any mathematical operation the result will also be NaN:

```javascript
NaN + 5; // NaN
```

You can test for NaN using the built-in isNaN() function:

```javascript
isNaN(NaN); // true
```

JavaScript also has the special values Infinity and -Infinity:

```javascript
1 / 0; //  Infinity
-1 / 0; // -Infinity
```

You can test for Infinity, -Infinity and NaN values using the built-in isFinite() function:

```javascript
isFinite(1/0); // false
isFinite(-Infinity); // false
isFinite(NaN); // false
```

> *The parseInt() and parseFloat() functions parse a string until they reach a character that isn't valid for the specified number format, then return the number parsed up to that point. However the "+" operator simply converts the string to NaN if there is an invalid character contained within it. Just try parsing the string "10.2abc" with each method by yourself in the console and you'll understand the differences better.*
