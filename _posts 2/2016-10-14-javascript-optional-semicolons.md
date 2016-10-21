---
date: '2016-10-14 21:12 +0200'
published: true
title: JavaScript - Optional Semicolons
category:
  - js
---
Like many programming languages, JS uses semicolon to separate statements. 

In JS you can usually omitt the semicolon between two statements if those are written in separate lines. You can also omitt at the end of a program or if the next token in the program is a closing curly brace. Many programmers always emitt the semicolan, but some omitts them when ever posible. 

**In general, if a statement begings with (, [, /, +, or -, there is a chance that if could be interpreted as a continuation of the statent.
Statements that begin with /,+ and - are quite rare in practice, but ( and [ are not uncommon. 
Some programmers like put protective semicolons in front of these statements.**

Consider the following code. Since the two statment apear on separate lines, the first semicolon could be omitted:

```javascript
a = 3;
b = 4;
```

Written as followed, you must however have the first semicolon.

```javascript
a = 3; b = 4;
```

JS does not treat every line break as a semicolon: it usually treats line breaks as semicolon only if it can't parse the code without the semicolons.

JS threats a line break as a semicolon if the next monospace character cannot be interpreted as a continuation of the current statement. Consider the following:

```javascript
var a 
a
=
3
console.log(a)
```

JS interprets this code like this:

```javascript
var a; a = 3; console.log(a);
```

JS does treat the first line break as a semicolon as it cannot pase the code "var a" without a semicolon.

The second a could stand alone as the statement a; but it can continue parsing the longer statement "a = 3". These statment terminating can leed to some suprising cases. This code looks like two separate statements separated with a new line.

```javascript
var y = x + f
(a+b).toString()
```

but the paranteses on the second line of code can be interpreted as a function invocation of f.

```javascript
var y = x + f(a+b).toString();
```

There are two exceptins to the genereal rule that JS interprets line breaks as semicolons when it cannot parse the secont line as a continuation of the statement on the first line. The first involves return, break and continue statements. These statement often stand alone, but thay are sometimes followed by an identifier or expression. If a line break appears efter any of these words, JS will always interpret that line break as a semicolon:

```javascript
return
true;
```

JS will assume you mean:

```javascript
return; true;
```

However you probably mean:

```javascript
return true;
```

This means you must not insert a line break between return, break or continue and the expression that follows the keyword.

The second exception involves the ++ and -- operators. These operators can be prefixed operators that appear before an expression or postfix that appear after an expression.

If you want to use these operators, you must insert them on the same line as the expression, otherwise the line break will be treated as a semicolon. They will otherwise be treated as a prefix:

```javascript
x
++
y
```

Is parsed as:

```javascript
x;
++Y;
```

and not as:

```javascript
x++; y;
```