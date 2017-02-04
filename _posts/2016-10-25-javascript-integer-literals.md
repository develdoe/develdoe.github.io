---
date: '2016-10-25 15:37 +0200'
published: true
title: Javascript - Integer Literals
category:
  - JavaScript
socialImg: js.png
---
A literal in programming is simply values you find in source code. 

Integers can be expressed in:

* decimal (base 10) 
* hexadecimal (base 16) 
* octal (base 8)
* binary (base 2).

**Decimal** integer literal consists of a sequence of digits without a leading 0 (zero).

```javascript
0
3
1000000
```

**Octal** is indicated by leading 0 (zero) on an integer literal, or leading 0o (or 0O). Octal integers can include only the digits 0-7.

```javascript
0377	// 3*64 + 7*8 + 7 = 255 (base-10)
```

 **Hexadecimal** is indicated by leading 0x (or 0X). Hexadecimal integers can include digits (0-9) and the letters a-f and A-F.

```javascript
0xff	  // 15*16 + 15 = 255 (base-10)
```

*Leading 0b (or 0B) indicates binary. Binary integers can include digits only 0 and 1.*

**Since somer implementation support octal literals and some do not, you should never write an integer literal with a leading 0. In EXMAScript 5, octals are explicitly forbidden.**

