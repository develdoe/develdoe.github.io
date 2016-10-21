---
date: '2016-10-14 20:57 +0200'
published: true
title: Javascript - Reserved Words
category:
  - js
---
JS reservs a number of identifiers as the keyword of the language itself.
You cannot use these words as identifierss in your programs

**Keep in mind that JS implementation may define other global variable and functions, 
and each specific JS embedding (client-side, server-side, etc) will have its own list of global properties.**

||||||
|-|-|-|-|-|
|break|delete|function|return|typeof|
|case|do|if|switch|var|
|catch|else|in|this|var|
|continue|false|instansof|throw|while|
|debugger|finally|new|true|with|
|default|for|null|try|||

JS also reserves certain keywords that are not currently used by the language but wich might be used in future versions. ECMAScrips 5 reservs the following:

||||||
|-|-|-|-|-|
|class|const|enum|export|extends|
|inport|super|||||

In addition, these words, wich are legal in ordanary JS, are reserved in strict mode:

||||||
|-|-|-|-|-|
|implements|let|private|public|yield|
|interface|package|protected|static|||

Srict mode also imposes restriction on the ose of these identifiers.
Thay are not reserved, but that are not allowed as variables, functinos, or parameter names:

||||||
|-|-|-|-|-|
|arguments|eval|||||

ECMAScript 3 reservs all the keywords of the java language, and although this has been relaxed in ECMAScript 5, you should still avoid them if you plan to use ECMA3:

||||||
|-|-|-|-|-|
|abstract|double|goto|native|static|
|boolean|enum|implements|package|super|
|byte|export|import|private|synchronized|
|char|extends|int|protected|throuws|
|class|final|interface|public|transient|
|const|float|lang|short|volatile|

JS predefines a number of global variables and functions, and you should avoid using their names for your variables and functions:

||||||
|-|-|-|-|-|
|rguments|encodeURI|Infinify|number|regEx|
|Array|encodeURIComponent|ifFinite|object|String|
|Boolean|Error|isNaN|parseFlot|SyntaxError|
|Date|eval|JSON|parsInt|TypeError|
|decodeURI|EvalError|Math|RangeError|undefined|
|decodeURIComponent|Function|NaN|ReferenceError|URIErrori|
