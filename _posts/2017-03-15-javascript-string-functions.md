---
date: '2017-03-15 13:15 +0100'
published: true
title: JavaScript - String functions
category:
  - JavaScript
---
> JS treats primitive values as objects.  

## toString()

Returns a string representing the specific object

```js
var x = new String('Hello world');

console.log(x.toString()); // logs 'Hello world'
```

## valueOf()

Same as toString()

```js
var x = new String('Hello world');
console.log(x.valueOf()); // Displays 'Hello world'
```

## length

Returns the length property:

```js
var str = "adsf"
console.log(str.length) // => 4
```

## trim()

Removes whitespace from both ends of a string. Whitespace in this context is all the whitespace characters (space, tab, no-break space, etc.) and all the line terminator characters (LF, CR, etc.) and returns a new string.

```js 
var orig = '   foo  ';
console.log(orig.trim()); // 'foo'

// Another example of .trim() removing whitespace from just one side.

var orig = 'foo    ';
console.log(orig.trim()); //
```

### polyfill

```js
if (!String.prototype.trim) {
  String.prototype.trim = function () {
    return this.replace(/^[\s\uFEFF\xA0]+|[\s\uFEFF\xA0]+$/g, '');
  };
}
```

---

## Extracing characters

### charAt(position)

Returns the character at a specified index:

```js
var str = "HELLO WORLD";
console.log(str.charAt(0)) // => H 
```

### charCodeAt()

Returns the unicode of the char at specified index:

```js
var str = "HELLO WORLD";
console.log(str.charAt(0)) // => 72
```

### codePointAt(pos) ES2015

Returns a non-negative integar that is the Unicode point value

**If there is no element at the specified position, undefined is returned.**

```js
'ABC'.codePointAt(1);          // => 66
'\uD800\uDC00'.codePointAt(0); // => 65536
'XYZ'.codePointAt(42); // => undefined
```

#### Polyfill

```js
/*! http://mths.be/codepointat v0.1.0 by @mathias */
if (!String.prototype.codePointAt) {
  (function() {
    'use strict'; // needed to support `apply`/`call` with `undefined`/`null`
    var codePointAt = function(position) {
      if (this == null) {
        throw TypeError();
      }
      var string = String(this);
      var size = string.length;
      // `ToInteger`
      var index = position ? Number(position) : 0;
      if (index != index) { // better `isNaN`
        index = 0;
      }
      // Account for out-of-bounds indices:
      if (index < 0 || index >= size) {
        return undefined;
      }
      // Get the first code unit
      var first = string.charCodeAt(index);
      var second;
      if ( // check if it’s the start of a surrogate pair
        first >= 0xD800 && first <= 0xDBFF && // high surrogate
        size > index + 1 // there is a next code unit
      ) {
        second = string.charCodeAt(index + 1);
        if (second >= 0xDC00 && second <= 0xDFFF) { // low surrogate
          // http://mathiasbynens.be/notes/javascript-encoding#surrogate-formulae
          return (first - 0xD800) * 0x400 + second - 0xDC00 + 0x10000;
        }
      }
      return first;
    };
    if (Object.defineProperty) {
      Object.defineProperty(String.prototype, 'codePointAt', {
        'value': codePointAt,
        'configurable': true,
        'writable': true
      });
    } else {
      String.prototype.codePointAt = codePointAt;
    }
  }());
}
```

---

## Search strings

### indexOf

Returns the index position of the first occurence of a specific text in a string:


```js
var str = "Please locate where 'locate' occurs"
console.log(str.indexOf("locate")) // => 7
```

### lastIndexOf

Returns the index position of the last occurence:

```js 
var str = "Please locate where 'locate' occurs"
console.log(str.lastIndexOf("locate")) // => 21
```

**Note: both indexOf methods return - 1 if the string is not found.**

> JavaScript counts positions from zero.
0 is the first position in a string, 1 is the second, 2 is the third ...

### search

Returns the position

```js
var str = "Please locate where 'locate' occurs"
console.log(str.search("locate") // => 7
```

**Note: indexOf and search() are equal, but search can take much more powerfull search values (like regular expressions).**

### localCompare(compareString[, locales[, optoions]])

Returns a number indicating whether a reference string comes before or after or is the same as the giving string in sort order.

*The new `locales` and `options` arguments let application specify the language whose sort order should be used and customize the behavior of the function.*

#### Examples

```js
// The letter "a" is before "c" yielding a negative value
'a'.localeCompare('c'); // -2 or -1 (or some other negative value)

// Alphabetically the word "check" comes after "against" yielding a positive value
'check'.localeCompare('against'); // 2 or 1 (or some other positive value)

// "a" and "a" are equivalent yielding a neutral value of zero
'a'.localeCompare('a'); // 0
```

##### Check for browser support for extended arguments

The locales and options arguments are not supported in all browsers yet. To check whether an implementation supports them, use the "i" argument (a requirement that illegal language tags are rejected) and look for a RangeError exception:

```js
function localeCompareSupportsLocales() {
  try {
    'foo'.localeCompare('bar', 'i');
  } catch (e) {
    return e.name === 'RangeError';
  }
  return false;
}
```

##### Using locales

The results provided by localeCompare() vary between languages. In order to get the sort order of the language used in the user interface of your application, make sure to specify that language (and possibly some fallback languages) using the locales argument:

```js
console.log('ä'.localeCompare('z', 'de')); // a negative value: in German, ä sorts before z
console.log('ä'.localeCompare('z', 'sv')); // a positive value: in Swedish, ä sorts after z
```

The results provided by localeCompare() can be customized using the options argument:

```js
// in German, ä has a as the base letter
console.log('ä'.localeCompare('a', 'de', { sensitivity: 'base' })); // 0

// in Swedish, ä and a are separate base letters
console.log('ä'.localeCompare('a', 'sv', { sensitivity: 'base' })); // a positive value
```

---

## includes(searchString)

determines whether one string may be found within another string, returning true or false

```js
var str = 'To be, or not to be, that is the question.';

console.log(str.includes('To be'));       // true
console.log(str.includes('question'));    // true
console.log(str.includes('nonexistent')); // false
console.log(str.includes('To be', 1));    // false
console.log(str.includes('TO BE'));       // false
```

#### Polyfill

```js
if (!String.prototype.includes) {
  String.prototype.includes = function(search, start) {
    'use strict';
    if (typeof start !== 'number') {
      start = 0;
    }
    
    if (start + search.length > this.length) {
      return false;
    } else {
      return this.indexOf(search, start) !== -1;
    }
  };
}
```

### endsWith(searchString[, position])

Deterines wghether a string ends with the character of another string, returns boolean.

```js
var str = 'To be, or not to be, that is the question.';

console.log(str.endsWith('question.')); // true
console.log(str.endsWith('to be'));     // false
console.log(str.endsWith('to be', 19)); // true
```

#### Polyfill

```js
if (!String.prototype.endsWith) {
  String.prototype.endsWith = function(searchString, position) {
      var subjectString = this.toString();
      if (typeof position !== 'number' || !isFinite(position) || Math.floor(position) !== position || position > subjectString.length) {
        position = subjectString.length;
      }
      position -= searchString.length;
      var lastIndex = subjectString.lastIndexOf(searchString, position);
      return lastIndex !== -1 && lastIndex === position;
  };
}
```

### startsWith(searchString[, posotion])

Determines whether a string begins with the caracter of another string and returns a boolean.

```js
var str = 'To be, or not to be, that is the question.';

console.log(str.startsWith('To be'));         // true
console.log(str.startsWith('not to be'));     // false
console.log(str.startsWith('not to be', 10)); // true
```

#### Polyfill

```js
if (!String.prototype.startsWith) {
    String.prototype.startsWith = function(searchString, position){
      position = position || 0;
      return this.substr(position, searchString.length) === searchString;
  };
}
```


### match(regex)

Retrieves the matching when matching is a string against a regex. Returns 
an array containting the entire match result and any parentheses-captured matched result, null it no match

If he regular expression does not include the g flag, str.match() will return the same result as RegExp.exec(). The returned Array has an extra input property, which contains the original string that was parsed. In addition, it has an index property, which represents the zero-based index of the match in the string.

If the regular expression includes the g flag, the method returns an Array containing all matched substrings rather than match objects. Captured groups are not returned.

#### Use instead

* If you need to know if a string matches a regular expression RegExp, use search().
* If you only want the first match found, you might want to use RegExp.exec() instead.
* if you want to obtain capture groups and the global flag is set, you need to use RegExp.exec() instead.

#### Examples

##### Using match()

```js
var str = 'For more information, see Chapter 3.4.5.1';
var re = /see (chapter \d+(\.\d)*)/i;
var found = str.match(re);

console.log(found);

// logs [ 'see Chapter 3.4.5.1',
//        'Chapter 3.4.5.1',
//        '.1',
//        index: 22,
//        input: 'For more information, see Chapter 3.4.5.1' ]

// 'see Chapter 3.4.5.1' is the whole match.
// 'Chapter 3.4.5.1' was captured by '(chapter \d+(\.\d)*)'.
// '.1' was the last value captured by '(\.\d)'.
// The 'index' property (22) is the zero-based index of the whole match.
// The 'input' property is the original string that was parsed.
```

##### Using global and ignore case flags with match()

```js
var str = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz';
var regexp = /[A-E]/gi;
var matches_array = str.match(regexp);

console.log(matches_array);
// ['A', 'B', 'C', 'D', 'E', 'a', 'b', 'c', 'd', 'e']
```

##### A non-RegExp object as the parameter

When the parameter is a string or a number, it is implicitly converted to a RegExp by using new RegExp(obj). If it is a positive number with a positive sign,the RegExp() method will ignore the positive sign. 

```js
var str1 = "NaN means not a number. Infinity contains -Infinity and +Infinity in JavaScript.",
    str2 = "My grandfather is 65 years old and My grandmother is 63 years old.",
    str3 = "The contract was declared null and void.";
str1.match("number");   // "number" is a string. returns ["number"]
str1.match(NaN);        // the type of NaN is the number. returns ["NaN"]
str1.match(Infinity);   // the type of Infinity is the number. returns ["Infinity"]
str1.match(+Infinity);  // returns ["Infinity"]
str1.match(-Infinity);  // returns ["-Infinity"]
str2.match(65);         // returns ["65"]
str2.match(+65);        // A number with a positive sign. returns ["65"]
str3.match(null);       // returns ["null"]
```

### normalize([form])

Returns the Unicode Normalization Form of a given string (if the value isn't a string, it will be converted to one first).

#### Prameter

form: One of "NFC", "NFD", "NFKC", or "NFKD", specifying the Unicode Normalization Form. If omitted or undefined, "NFC" is used.

* NFC — Normalization Form Canonical Composition.
* NFD — Normalization Form Canonical Decomposition.
* NFKC — Normalization Form Compatibility Composition.
* NFKD — Normalization Form Compatibility Decomposition.

```js
// Initial string

// U+1E9B: LATIN SMALL LETTER LONG S WITH DOT ABOVE
// U+0323: COMBINING DOT BELOW
var str = '\u1E9B\u0323';


// Canonically-composed form (NFC)

// U+1E9B: LATIN SMALL LETTER LONG S WITH DOT ABOVE
// U+0323: COMBINING DOT BELOW
str.normalize('NFC'); // '\u1E9B\u0323'
str.normalize();      // same as above


// Canonically-decomposed form (NFD)

// U+017F: LATIN SMALL LETTER LONG S
// U+0323: COMBINING DOT BELOW
// U+0307: COMBINING DOT ABOVE
str.normalize('NFD'); // '\u017F\u0323\u0307'


// Compatibly-composed (NFKC)

// U+1E69: LATIN SMALL LETTER S WITH DOT BELOW AND DOT ABOVE
str.normalize('NFKC'); // '\u1E69'


// Compatibly-decomposed (NFKD)

// U+0073: LATIN SMALL LETTER S
// U+0323: COMBINING DOT BELOW
// U+0307: COMBINING DOT ABOVE
str.normalize('NFKD'); // '\u0073\u0323\u0307'
```

---

## Extracting

* slice(start, end)
* substring(start, end)
* substr(start, length)

### slice(start,end)

Returns a extracted part in a new string. 

```js
var str = "apple, banana, kiwi"
console.log(str.slice(7,13)) // => banana
```

If you omit the second param, the method will slice out the rest of the string:

```js
var str = "apple, banana, kiwi"
console.log(str.slice(7)) // => banana, kiwi
```

or the get the last 12 characters:

```js
var str = "apple, banana, kiwi"
var res = str.slice(-12) // => banana, kiwi
```

### substring(start, end)

The only differens is that substring cannot use negative indexes.

```js
var str = "apple, banana, kiwi"
console.log(str.slice(7,13)) // => banana
```

### substr(start, length)

Similar to slice, only last param dictates lenght of the extraction.

```js
var str = "apple, banana, kiwi"
console.log(str.slice(7,6)) // => banana
```


**Note: if the first start parameter is negative the position is counted from the end of the strin & if the second param is omited the rest is sliced out.**

```js
var str = "apple, banana, kiwi"
console.log(str.slice(-4)) // => kiwi
```

---

## Replacing & repeating

### repace()

Replaces with parameter value:

```js
var str = "apple, banana, kiwi"
console.log(str.replace("banana","rasberry")) // => apple, rasberry, kiwi
```

Can also take Regular Expressions.

```js
var str = "apple, banana, kiwi"
console.log(str.replace(/banana/g,"rasberry")) // => apple, rasberry, kiwi
```

**Note: replace returns a new string with the new value.**

### repeat(count)

Constructs and returns a new string which contains the specified number of copies of the string on which it was called, concatenated together.

```js
'abc'.repeat(-1);   // RangeError
'abc'.repeat(0);    // ''
'abc'.repeat(1);    // 'abc'
'abc'.repeat(2);    // 'abcabc'
'abc'.repeat(3.5);  // 'abcabcabc' (count will be converted to integer)
'abc'.repeat(1/0);  // RangeError

({ toString: () => 'abc', repeat: String.prototype.repeat }).repeat(2);
// 'abcabc' (repeat() is a generic method)
```

#### Polyfill

```js
if (!String.prototype.repeat) {
  String.prototype.repeat = function(count) {
    'use strict';
    if (this == null) {
      throw new TypeError('can\'t convert ' + this + ' to object');
    }
    var str = '' + this;
    count = +count;
    if (count != count) {
      count = 0;
    }
    if (count < 0) {
      throw new RangeError('repeat count must be non-negative');
    }
    if (count == Infinity) {
      throw new RangeError('repeat count must be less than infinity');
    }
    count = Math.floor(count);
    if (str.length == 0 || count == 0) {
      return '';
    }
    // Ensuring count is a 31-bit integer allows us to heavily optimize the
    // main part. But anyway, most current (August 2014) browsers can't handle
    // strings 1 << 28 chars or longer, so:
    if (str.length * count >= 1 << 28) {
      throw new RangeError('repeat count must not overflow maximum string size');
    }
    var rpt = '';
    for (;;) {
      if ((count & 1) == 1) {
        rpt += str;
      }
      count >>>= 1;
      if (count == 0) {
        break;
      }
      str += str;
    }
    // Could we try:
    // return Array(count + 1).join(this);
    return rpt;
  }
}
```

---

## Converting

### toUpperCase()

Converts to upper case:

```js
var str = "case"
console.log(str.toUpperCase()) // => CASE
```

### toLowerCase()

Converts to lower case:

```js
var str = "CASE"
console.log(str.toLowerCase()) // => cas
```

----

## Merge strings

### concat(separator, str,...)

Joins two or more strings:

```js
var text1 = "Hello";
var text2 = "World";
console.log(text1.concat(" ", text2)) // => Hello World
```

**Note: concat returns a new string with the new value.**

---


## Converting to array

### split(char)

```js
var txt = "a,b,c,d,e";   // String
txt.split(",");          // Split on commas
txt.split(" ");          // Split on spaces
txt.split("|");          // Split on pipe 
var str = "Hello";       // String
str.split("");           // Split in characters 
```

If separator is omitted, the returned array will contain the whole string at index 0

**Accessing a string as an array is posible, however,  is unpredictable and you should not do it! Convert the string like above first!**

