---
date: '2016-10-14 20:44 +0200'
published: true
title: ''
category:
  - js
---
JS ignores spaces that appear between tokens in programs. For the most part JS also ignores line breaks.

In addition to the regular space character (\u0020) JS also recognizes tab (\u0009), and any other Unicode cat Zs as whitespace. JS recognizes the following characters as line terminators:

* line feed (\u000A)
* carriage return (\u000D)
* line separator (\u2028)
* paragraph separator (\u2029)

A Carrage return and line feed sequence is treated as a single line terminator. Unicode format control (cat Cf e.g. RIGHT-TO-LEFT) control the visual presentation of the text thay occur in.
Thay are important for the proper display of some non-English languages and are allowed in JS comments, string literals, and regular expressions literals, but not in the identifiers. 

Special case for ZERO WIDTH JOINER and ZERO WIDTH NON-JOINERS are allowed for identifiers, but not as the first character.

