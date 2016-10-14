---
date: '2016-10-14 20:49 +0200'
published: true
title: JavaScript - Normalization
category:
  - js
---

Unicode allows more than one way of encoding the same character. 

The string "é" can be encoded as tht single Unicode character \u0oE9 or as the regular ASCII e followed by the accent mark \u0301.

The two encoding may look exactly the same in a text editor, but thay have different binary encoding and are considered different by the computer. Unicode standard defines the prefered encoding for all characters and specifies a normalization procedure to convert text to a cronical form suitable for comparison.

JS assumes that the source code it interpreting has already been normalized and makes no attempt to normalize identifiers, strings, or regular expressions itself.
 
