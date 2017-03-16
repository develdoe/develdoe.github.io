---
date: '2017-03-16 06:14 +0100'
published: true
title: JavaScript - Regular Expressions
category:
  - JavaScript
---

A regular expression is an object that describes a pattern of characters.

## Syntax

`/pattern/modifiers; `

## Example 

```
var patt = /develdevils/i
```

## Modifiers

i = Case-insensitive
g = Global match (find all matches rather then stopping after the first)
m = Multiline matching

## Brackets

Brackets are used to find a range of characters. 

[abc]  = Find any character between the brackets
[^abc] = Find any character NOT between the brackets
[0-9]  = Find any character between the brackets (digits)
[x|y]  = Find any of the alternatives specified

## Metacharacters

Metacharacters are characters with spacial meaning.

.	= find a single character, except newline or line terminator
\w	= find a word character
\W 	= find a non-word character
\d	= find a digt
\D 	= find a non-digit character
\s	= find a whitespace character
\S 	= find a non-whitespace character