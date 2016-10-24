---
date: 2016-03-01T10:18:00.000Z
title: 'JavaScript - Overview '
description: >-
  This article goes through the JavaScript language and shows you things like
  comments, semicolon, the unicode character set, types, values, and variables.
published: true
category:
  - js
socialImg: js.png
---
JavaScript is an object-oriented dynamic language with types and operators, standard built-in objects, and methods. 

Its syntax is based on the Java and C languages — so many structures from those languages apply to JavaScript as well. 

One of the key differences is that JavaScript does not have classes; instead, the class functionality is accomplished by object prototypes (see more about ES6 Classes).The other main difference is that functions are objects, giving functions the capacity to hold executable code and be passed around like any other object.


{% highlight js %}
// Anyting following double slashes is an English-Language comment.
// Read Comments carfully: they explain tghe JS code.

// A variable is a symbolic name for a value.
// Variables are declared with the var keyword:
var x;				// Declare a variable named x.

// Values can be assigned to variables with an = sign.
x = 0;				// Now the variable x has the value 0.
x				// => 0: A variable evaluates to its value.

// JS supports several types of values.
x = 1;				// Numbers.
x = 0.1;			// Just one Number for integers and reals.
x = "Hello World";		// Strings of text in quotation marks.
x = 'JavaScript';		// Single quote marks also delimit strings.
x = trye;			// Boolean value.
x = false;			// the other Boolean value.
x = null;			// Null is a special value means "no value".
x = undefined;			// Undefined is like null.

/*
 * Two inportant types that JS programs manipulate are objects and arrays.
 */

// JS's most importat data type is object. An object is an collection of name/value
// pair, or a string value map.
var book = {			// objects are enclosed in curley braces.
	topic: "JavaScript",	// The property "topic" has the value "JavaScript".
	fat: true		// The propery "true" has the value true.
};				// The brace marks the end of the object.

// Access the property of an object with . or []:
book.topic			// => "JavaScript"
book["fat"]			// => true: another way to access property values.
book.author = "Flanagan";	// Create a new property by assignment.
book.contents = {};		// {} is an empty object with no properties.

// JS also supports arrays (numrically indexed lists) of values:
var primes = [2, 3, 5, 7];	// An array of 4 values, delimited with [ and ].
primes[0]			// => 2: The first element (index 0) of the array.
primes.length			// => 4: how many elements in the array.
primes[primes.length-1]		// => 7: the last element of the array
primes[4] = 9;			// add a new element by assignment.
primes[4] = 11;			// Or alter an existing element by assignment.
var empty = [];			// [] is an empty array with no elements.
empry.length			// => 0

// Arrays and objects can hold other arrays and objects:
var point = [			// An array with 2 elements.
	{x:0, y:0},		// Each element is an object.
	{x:1, y:1}
];

var data = {			// An object with 2 properties.
	trail1: [[1,2], [3,4]],	// The value of each property is an array
	trail2: [[2,3], [4,5]]	// The elements of the arrays is arrays.
};

/* The syntax above listing array elements within square braces or mapping object
 * property names to property values inside curly braces is known as an initializer
 * expression, and it is just one of the topics in chapter 4, Expressions and Operators.
 * An expression is a phrase of JS that can be evaluated to produce a value.
 * The use of . and [] tp refer to the value of an object property or array element is an expression.
 * => shows that is is an valuation of an expression.
 *
 * One of the nost common ways to form expressions in JS is to use operators like these:
 */
// Oerators act on values (the operands) to produce a new value.
// Aritmetic operators are the nost common:
3 + 2 				// => 5: addition
3 - 2				// => 1: subtraction
3 / 2				// => 1.5: division
point[1].x - point[0].x		// => 1: more complicated operands also work.
"3" + "2"			// =>"32": adds numbers, concatinates strings

// JS defines some shorthand aritmetic operators
var count = 0;			// Defines a variable
count++;			// Increment the variable
count--;			// Decrement the variable
count += 2;			// Add 2: same as count = count + 2;
count *= 3;			// Multiply by 3: same as count = count * 3;
count 				// => 6: variable names are expressions, too.

// Equality and relational operators test whether two values are equal,
// unequal, less than, greater than, and so on. They evaluates to true or false.
var x = 2, y = 3;		// These = signs are assignemnt, not equality tests.
x == y				// => false: equality
x != y				// => true: inequality
x < y				// => true: less-than
x >= y				// => trye: less-than or equal
x > y				// => false: greater-than
x >= Y				// => false: greater-than or equal
"two" == "three"		// => false: the two strings are different
"two" > "three"			// => true: "tw" is alphabetically greater than "th"
false == (x > y)		// => true: false is equal to false

// Loguical operators combine or invert boolan values
(x == 2) && (y == 3)		// => true: both the comparison are true. && is AND
(x > 3) || (y < 3)		// => false: neither comparison is true. || is OR
!(x == y)			// => true: ! inverts a boolean value

/* If the phrases of JS is expressions then the full sentences are statements.
 * In the code above, the lines that end with semicolons are statements. (in the
 * code below, you'll see multiline statements that do not end with semicolons.)
 * There is actually a lot of overlap between statements and expressions. Roughly, an
 * expression is something that computes a value but doesn't do anything: it doesn't
 * alter the code state in any way. Statements doesn't have a value (that we care about),
 * but thay do alter the state. The other broad category of statements is control structures,
 * such as conditionals and loops. Example below, after we cover functions.
 */

/* A function is a named parameterized block of JS code that you define once, and can then
 * invoke over and over again.
 */
// Functions are parameterized block of JS code that we can invoke.
function plus1(x) {		// Defines a function named plus1 with parameter x.
	return x + 1;		// return a value one larger thatn the value passed in.
};				// Functions are enclosed in curly braces.

plua1(y);			// => 4: y is 3, so this invocation returns 3 + 1.

var square = funtion(x) {	// Function are values, and can be assigned to vars
	return x * x;		// Compute the function's value.
};				// Semicolon marks the end of the assignment

square(plus1(y))		// => 16: invoke two functions in one expression.

// When functions are assigned to the property of an object, we call
// them "methods". All JS objects have methods:
var a = [];			// Create an empty array
a.push(1,2,3);			// The push() method adds element to an array.
a reverse();			// Anpther method: revers the order of the elements.

// We can define our own methods, too. The "this" keyword refers to the object.
// on lwhich the method is defined: in this case, the points array from above.
points.dist = function(){	// Defines a method to compute distances between points
	var p1 = this[0];	// First element of the array we're invoked on.
	var p2 = this[1];	// Second element of the "this" object.
	var a = p2.x - p1.x;	// Difference in X cordinates.
	var b = p2.y - p1.y;	// Difference in Y cordinates.
	return Math-sqrt(a*a +	// The Pythagorean theorem
			b*b);	// Math.sqrt() computes the square root.
};
points.dist();			// => 1.414: distance between our two points

/*
 * Function whos bodies demonstrate common JS control structures statements:
 */
// JS statements include conditiional and loops using the syntax of c, c++, and other languages:
function abs(x) {		// A function to compute the absolute value.
	if (x > 0) {		// The if statement...
		return x;	// executes this code if the comparison is true.
	}			// end of the if clause.
	else {			// executes if comparison is false
		return -x;
	}			// Curly braces is optional if only 1 statement per clause
};				// Note return statement nested inside if/else

function factorial(n) {		// A function to compute factorials
	var product = 1;	// Start with the product of 1
	while(n > 1) {		// Repeat statements in {} while expr in () is true
		product *= n;	// Shortcut for n = n -1
	}			// end of loop
	return product;		// return the product.
};
factorial(4);			// => 24: 1*4*3*2

function factorial2(n) {	// Another version using a different loop
	var i, product = 1;	// Start with 1
	for(i=2, i < n, i++)	// Automatically increment i fron 2 up to n
		product *=1	// Do this each time. {} not needed for 1-line loops
	return product;		// return fratorial.
}
fractorial(5);			// => 120: 1*2*3*4*5

/*
 * JS is an object-oriented language, but is quite different than most.
 * Here is a very simple example that demonstrates how to define a JS class to
 * represent 2D geometric points. Objects that instances of this class have a
 * single method named r() that computs the distane of the point from the origin
 */
// Define a consturcor function to initialize a new Point object:
function Point(x,y) {		// By convention, constructors start with Cap
	this.x = x;		// this keyword is the new object being initialized.
	this.y = y;		// Store function arguments as object properties.
};				// No return is necessary.

// Use an constructor functin with keyword "new" to create instances
var p = Point(1,1);		// The geometric point (1,1)

// Define methods for Point objects by assigning them to the prototype
// object associated with the constructor funtion.
Point.prototype.r = function() {
	return Math.sqrt(	  // Return the square root of x^2 + y^2
		this.x * this.x	+ // This is the Point obhect on wich the method ...
		this.y * this.y	  // ... is invoked
	);
};

// Now the Point object p (and all future Point objects) inherits the method r()
p.r();				// => 1.414...
{% endhighlight %}
