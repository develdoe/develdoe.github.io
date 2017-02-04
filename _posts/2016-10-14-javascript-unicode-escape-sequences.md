---
date: '2016-10-14 20:47 +0200'
published: true
title: JavaScript - Unicode Escape Sequences
category:
  - JavaScript
---
Some computer hardware and software can not display or input the full set of Unicode.

To support programmers using this older technology, JS defines special sequence of six ASCII characters to represent any 16 bit Unicode codepoint. These Unicode escapes begin with \u and are followed by exactly four hexadecimal digits. 

Unicode escapes may appear in JS string literals, regular expressions literals, and in identifiers (but not in language keywords).

```javascript
"cafe" === "caf\u00e9"	// => true
```