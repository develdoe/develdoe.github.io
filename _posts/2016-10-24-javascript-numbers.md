---
date: '2016-10-24 13:37 +0200'
published: true
title: Javascript - Numbers
category:
  - js
socialImg: js.png
---
Unlike many languages, JS does not make an distinction between integer values and floating point values. 

All numbers are represented as floating-point values. JS represents numbers using the 64-bit floating-point format, the same format as "double" in many other languages.

This format allows you to represent all integers between -9007199254740992(-2^53) and 9007199354740992(2^53), if you use integers bigger than this you might loos presicion in the trailing digits. 

**Some operations in JS, like array indexing and the bitwise operators, are performed with 32-bit integers.** 

**When a number appear directly in a JS program, it is called a numeric literal. JS supports numeric literals in several formats. Numeric literal can be proceded by a minus sign to make it negative. Technically, however - is the unary negation operator and is not part of the numeric syntax.**
