---
date: '2017-03-16 06:14 +0100'
published: true
title: JavaScript - Regular Expressions
category:
  - JavaScript
---

 > A regular expression, regex or regexp (sometimes called a rational expression)is, in theoretical computer science and formal language theory, a sequence of characters that define a search pattern. Usually this pattern is then used by string searching algorithms for "find" or "find and replace" operations on strings (2017, wikipedia).
 
## Syntax

`/pattern/modifiers; `


## Working with regular expressions

Regular expressions are used with the RegExp methods test and exec and with the String methods match, replace, search, and split.

When you want to know whether a pattern is found in a string, use the test or search method; for more information (but slower execution) use the exec or match methods. 

### Methods 

|method|desc|
|--|--|
|exec|Executes a search for a matching in a string. It returns an array of information or null on a missmatch.|
|test|Tests for a match in a string and returns a boolean|
|match|Executes a search for a matching in a string and returns array of information or null.|
|search|Tests for a mach in a string and returns the index of the match or -1.|
|repleace|Executes a search for match in a string and replaces the substring with a replacement substring.|
|split|Uses a regular expression or fixed string to break a string into an array of substrings.|

If you use exec or match and if the match succeeds, these methods return an array and update properties of the associated regular expression object and also of the predefined regular expression object, RegExp. 

If the match fails, the exec method returns null (which coerces to false).

### Examples 

**Find a match**

```js
var myRe = /d(b+)d/g
var myArray = myRe.exec(' cdbbdb sbz')
console.log(myArray) // => ["dbbd", "bb"]
```

**If you do not need to access the properties of the regular expression**
* If you need to use a method like indexOf on the regular expression at a later state, you cannot use this syntax*

```js
var myArray = /d(b+)d/g.exec('cdbbdbsbz')
```

**Equivalent**

```js
var myArray = "cdbbdbsbz".match(/d(b+)d/g)
```

**construct the regular expression from a string**

```js
var myRe = new RegExp('d(b+)d', 'g')
var myArray = myRe.exec('cdbbdbsbz')
```

### Using parenthesized substring matches

Including parentheses in a regular expression pattern causes the corresponding submatch to be remembered. For example, /a(b)c/ matches the characters 'abc' and remembers 'b'. To recall these parenthesized substring matches, use the Array elements [1], ..., [n].

The number of possible parenthesized substrings is unlimited. The returned array holds all that were found. The following examples illustrate how to use parenthesized substring matches.

The following script uses the replace() method to switch the words in the string. For the replacement text, the script uses the $1 and $2 in the replacement to denote the first and second parenthesized substring matches.

```js
var re = /(\w+)\s(\w+)/;
var str = 'John Smith';
var newstr = str.replace(re, '$2, $1');
console.log(newstr); // => "Smith, John"
```

### Advanced searching with flags

Regular expressions have five optional flags that allow for global and case insensitive searching. These flags can be used separately or together in any order, and are included as part of the regular expression.

|flag|description|
|--------|-----------|
|i|Case-insensitive|
|g|Global match (find all matches rather then stopping after the first)|
|m|Multiline matching|
|u|unicode; treat pattern as a sequene of unicode code points|
|y|Perform a "sticky" search that matches starting at the current position in the target string|

```js
var re = /pattern/flags;
```

```js
var re = new RegExp('pattern', 'flags');
```

*The behavior associated with the 'g' flag is different when the .exec() method is used.  (This should not be a surprise; a given regular expression is expected to have different behavior when used with .match() versus .exec().  The roles of "class" and "argument" get reversed: In the case of .match(), the string class (or data type) owns the method and the regular expression is just an argument, while in the case of .exec(), it is the regular expression that owns the method, with the string being the argument.  Contrast str.match(re) versus re.exec(str).)  The 'g' flag is used with the .exec() method to get iterative progression.*

```js
var xArray; while(xArray = re.exec(str)) console.log(xArray);
// produces: 
// ["fee ", index: 0, input: "fee fi fo fum"]
// ["fi ", index: 4, input: "fee fi fo fum"]
// ["fo ", index: 7, input: "fee fi fo fum"]
```

## Brackets

Brackets are used to find a range of characters. 

|expression|description|
|--------|-----------|
|[abc]|Find any character between the brackets|
|[^abc]|Find any character NOT between the brackets|
|[0-9]|Find any character between the brackets (digits)|
|[x|y]|Find any of the alternatives specified|

## Metacharacters

Metacharacters are characters with spacial meaning.

|metacharacter|description|
|--------|-----------|
|.|find a single character, except newline or line terminator|
|\w|find a word character|
|\W|find a non-word character|
|\d|find a digt|
|\D|find a non-digit character|
|\s|Matches a single white space character, space, tab,form feed, line| feed|
|\S|Matches a single character other than white space|
|\b|find a match at the beginning/end of a word|
|\B|find a match not at the beginning/end of a word|
|\O|find a NUL character|
|\n|find a new line character|
|\f|find a form feed character|
|\r|find a carriage return character|
|\t|find a tab char|
|\v|find vertical tab char|
|\xxx|find the char specified by an octal number|
|\xdd|find the character specified by a hexadecimal nuber dd|
|\uxxx|Find the Unicode character specified by a hexadecimal number xxxx|

## Quantifiers

|quantifier|description|
|--------|-----------|
|n+|Match any string that contains at leas one *n*|
|n*|Match any string that contains zero and more *n*|
|n?|Match any string that contains zero or one occurences of *n*|
|n{X}|Matches any string that contains a sequence of X n's|
|n{X,Y}|Matches any string that contains a sequence of X to Y n's|
|n{X,}|Matches any string that contains a sequence of at least X n's|
|n$|Matches any string with n at the end of it|
|^n|Matches any string with n at the beginning of it|
|?=n|Matches any string that is followed by a specific string n|
|?!n|Matches any string that is not followed by a specific string n|

## RegExp Object Properties

|Property|Description|
|--------|-----------|
|constructor|returns the function that created the RegExp objects prototype|
|global|checks whether the "g" modifier is set|
|ignoreCase|checks whether the "i" modifier is set|
|lastIndex|specified the index at which to start the next match|
|multiline|checks whether the "m" modifier is set|
|source|return the text of the regEx pattern|

## Examples

### Changing the order in an input string

The following example illustrates the formation of regular expressions and the use of string.split() and string.replace(). 

* It cleans a roughly formatted input string containing names (first name first) separated by blanks, tabs and exactly one semicolon. 
* Finally, it reverses the name order (last name first) and sorts the list.

```js
// The name string contains multiple spaces and tabs,
// and may have multiple spaces between first and last names.
var names = 'Harry Trump ;Fred Barney; Helen Rigby ; Bill Abel ; Chris Hand ';

var output = ['---------- Original String\n', names + '\n'];

// Prepare two regular expression patterns and array storage.
// Split the string into array elements.

// pattern: possible white space then semicolon then possible white space
var pattern = /\s*;\s*/;

// Break the string into pieces separated by the pattern above and
// store the pieces in an array called nameList
var nameList = names.split(pattern);

// new pattern: one or more characters then spaces then characters.
// Use parentheses to "memorize" portions of the pattern.
// The memorized portions are referred to later.
pattern = /(\w+)\s+(\w+)/;

// New array for holding names being processed.
var bySurnameList = [];

// Display the name array and populate the new array
// with comma-separated names, last first.
//
// The replace method removes anything matching the pattern
// and replaces it with the memorized string—second memorized portion
// followed by comma space followed by first memorized portion.
//
// The variables $1 and $2 refer to the portions
// memorized while matching the pattern.

output.push('---------- After Split by Regular Expression');

var i, len;
for (i = 0, len = nameList.length; i < len; i++) {
  output.push(nameList[i]);
  bySurnameList[i] = nameList[i].replace(pattern, '$2, $1');
}

// Display the new array.
output.push('---------- Names Reversed');
for (i = 0, len = bySurnameList.length; i < len; i++) {
  output.push(bySurnameList[i]);
}

// Sort by last name, then display the sorted array.
bySurnameList.sort();
output.push('---------- Sorted');
for (i = 0, len = bySurnameList.length; i < len; i++) {
  output.push(bySurnameList[i]);
}

output.push('---------- End');

console.log(output.join('\n'));
```

### Using special characters to verify input

In the following example, the user is expected to enter a phone number. When the user presses the "Check" button, the script checks the validity of the number. If the number is valid (matches the character sequence specified by the regular expression), the script shows a message thanking the user and confirming the number. If the number is invalid, the script informs the user that the phone number is not valid.

```html
<!DOCTYPE html>
<html>  
  <head>  
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">  
    <meta http-equiv="Content-Script-Type" content="text/javascript">  
    <script type="text/javascript">  
      var re = /(?:\d{3}|\(\d{3}\))([-\/\.])\d{3}\1\d{4}/;  
      function testInfo(phoneInput) {  
        var OK = re.exec(phoneInput.value);  
        if (!OK)  
          window.alert(phoneInput.value + ' isn\'t a phone number with area code!');  
        else
          window.alert('Thanks, your phone number is ' + OK[0]);  
      }  
    </script>  
  </head>  
  <body>  
    <p>Enter your phone number (with area code) and then click "Check".
        <br>The expected format is like ###-###-####.</p>
    <form action="#">  
      <input id="phone"><button onclick="testInfo(document.getElementById('phone'));">Check</button>
    </form>  
  </body>  
</html>
```

<div>
Within non-capturing parentheses (?: , the regular expression looks for three numeric characters \d{3} OR | a left parenthesis \( followed by three digits \d{3}, followed by a close parenthesis \), (end non-capturing parenthesis )), followed by one dash, forward slash, or decimal point and when found, remember the character ([-\/\.]), followed by three digits \d{3}, followed by the remembered match of a dash, forward slash, or decimal point \1, followed by four digits \d{4}.
</div>