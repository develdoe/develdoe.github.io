---
date: '2017-03-16 06:14 +0100'
published: true
title: JavaScript - Regular Expressions
category:
  - JavaScript
---

 > A regular expression, regex or regexp (sometimes called a rational expression)is, in theoretical computer science and formal language theory, a sequence of characters that define a search pattern. Usually this pattern is then used by string searching algorithms for "find" or "find and replace" operations on strings (2017, wikipedia).

## Working with regular expressions

Regular expressions are used with the RegExp methods test and exec and with the String methods match, replace, search, and split.

### Methods 

|method|desc|
|--|--|
|exec|Executes a search for a matching in a string. It returns an array of information or noull on a missmatch.|
|test|Tests for a match in a string and returns a boolean|
|match|Executes a search for a matching in a string and returens rray of information or null.|
|search|Tests for a mach in a string and returns the index of the match or -1.|
|repleace|Executes a search for match in a string and replaces the substring with a replacement substring.|
|split|Uses a regular expression or fixed string to break a string into an array of substrings.|

## Syntax

`/pattern/modifiers; `

## Example 

```
var patt = /develdevils/i
```

## Modifiers

`i` = Case-insensitive
`g` = Global match (find all matches rather then stopping after the first)
`m` = Multiline matching

## Brackets

Brackets are used to find a range of characters. 

`[abc]`  = Find any character between the brackets
`[^abc]` = Find any character NOT between the brackets
`[0-9]`  = Find any character between the brackets (digits)
`[x|y]`  = Find any of the alternatives specified

## Metacharacters

Metacharacters are characters with spacial meaning.

`.`	    = find a single character, except newline or line terminator
`\w`    = find a word character
`\W` 	= find a non-word character
`\d`	= find a digt
`\D` 	= find a non-digit character
`\s`	= Matches a single white space character, space, tab,form feed, line feed. 
`\S` 	= Matches a single character other than white space
`\b`    = find a match at the beginning/end of a word
`\B`    = find a match not at the beginning/end of a word
`\O`    = find a NUL character
`\n`    = find a new line character
`\f`    = find a form feed character
`\r`    = find a carriage return character
`\t`    = find a tab char
`\v`    = find vertical tab char
`\xxx`  = find the char specified by an octal number
`\xdd`  = find the character specified by a hexadecimal nuber dd
`\uxxx` = Find the Unicode character specified by a hexadecimal number xxxx

## Quantifiers

`n+`    = Match any string that contains at leas one *n*
`n*`    = Match any string that contains zero and more *n*
`n?`    = Match any string that contains zero or one occurences of *n*
`n{X,Y} = 