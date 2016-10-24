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

