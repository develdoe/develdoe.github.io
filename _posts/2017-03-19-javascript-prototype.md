---
date: '2017-03-19 23:08 +0100'
published: true
title: JavaScript - Prototype
category:
  - JavaScript
---
Every JavaScript object has a prototype and the prototype is also an object. 

The Object.prototype property represents the Object prototype object.

All JavaScrpit object inherit their properties and methods from their prototype.

Nearly all objects in JavaScript are instances of Object.

A typical object inherits properties (including methods) from Object.prototype, although these properties may be shadowed (a.k.a. overridden).

However, an Object may be deliberately created for which this is not true (e.g. by Object.create(null)), or it may be altered so that this is no longer true (e.g. with Object.setPrototypeOf).

Objects created using an object literal, or with new Object(), inherit from a prototype called Object.prototype.

Changes to the Object prototype object are seen by all objects through prototype chaining, unless the properties and methods subject to those changes are overridden further along the prototype chain. 

This provides a very powerful although potentially dangerous mechanism to override or extend object behavior.


## Creating a Prototype

The strandard way to create an object protoype is to use an object constructor function: 

```js
function Person(first,last,age,eyecolor) {
  this.firstName = first
  this.lastName = last
  this.age = age
  this.eyeColor = eyecolor
}

var father = new Person('Thomas','Fors',66,'blue')

console.log(father)
```

**The constructor function is the prototype for Person object. It is considered good practice to name constructor functions with an upper-case first letter.**

## Using the prototype Property

The JavaScript prototype property allows you to add new properties to an existing prototype:

```js
function Person(first, last, age, eyecolor) {
    this.firstName = first;
    this.lastName = last;
    this.age = age;
    this.eyeColor = eyecolor;
}
Person.prototype.nationality = "English"; 
```

The JavaScript prototype property also allows you to add new methods to an existing prototype:

```js
function Person(first, last, age, eyecolor) {
    this.firstName = first;
    this.lastName = last;
    this.age = age;
    this.eyeColor = eyecolor;
}
Person.prototype.name = function() {
    return this.firstName + " " + this.lastName;
}; 
```

**Only modify your own prototypes. Never modify the prototypes of standard JavaScript objects!**

## Without using the prototype property

Adding a new property to an existing object is easy:

```js
father.nationality = "Swedish"
```

However, this property will be added to father and not to any other person objects.

Adding a new method to an existing object is also easy: 

```js
father.name = function () {
  return this.firstName + ' ' + this.lastName
}
```

However, this method will also jsut be added to father and not to any other person objects.


You cannot add a new property to a prototype the same way as you add a new property to an existing object, because the prototype is not an existing object.

To add a new property to a prototype, you must add it to the constructor function:

```js
function Person(first, last, age, eyecolor) {
    this.firstName = first;
    this.lastName = last;
    this.age = age;
    this.eyeColor = eyecolor;
    this.nationality = "English"; // can have default values
} 
```

Your constructor function can also define methods:

```js
function Person(first, last, age, eyecolor) {
    this.firstName = first;
    this.lastName = last;
    this.age = age;
    this.eyeColor = eyecolor;
    this.name = function() {return this.firstName + " " + this.lastName;};
} 
```

## Methods

### Object.prototype.hasOwnProperty()

Returns a boolean indicating whether an object contains the specified property as a direct property of that object and not inherited through the prototype chain.

#### Syntax

```
obj.hasOwnProperty(prop)
```

##### Parameters

###### prop

The String name or symbol of the property to test.

##### Return value

A Boolean indicating whether or not the object has the specified property.

#### Description

Every object descended from Object inherits the hasOwnProperty method. This method can be used to determine whether an object has the specified property as a direct property of that objec

#### Examples

Using hasOwnProperty to test for a property's existence

```js
obj = new Object()
obj.prop = 'exist'

function changeObj () {
  obj.newprop = obj.prop
  delete obj.prop
}

console.log(obj.hasOwnProperty('prop'))
changeObj()
console.log(obj.hasOwnProperty('prop'))
```

### Object.prototype.isPrototypeOf()

The isPrototypeOf() method checks if an object exists in another object's prototype chain.

#### Syntax

```
prototypeObj.isPrototypeOf(object)
```

##### Parameters

###### object

The object whose prototype chain will be searched.

##### Return value

A Boolean indicating whether the calling object has the specified object in its prototype chain.

##### Errors thrown

###### TypeError

A TypeError is thrown if prototypeObj is undefined or null.

#### Description

The isPrototypeOf() method allows you to check whether or not an object exists within another object's prototype chain.

#### Examples

This example demonstrates that Baz.prototype, Bar.prototype, Foo.prototype and Object.prototype exist in the prototype chain for object baz:

```js
function Foo() {}
function Bar() {}
function Baz() {}

Bar.prototype = Object.create(Foo.prototype);
Baz.prototype = Object.create(Bar.prototype);

var baz = new Baz();

console.log(Baz.prototype.isPrototypeOf(baz)); // true
console.log(Bar.prototype.isPrototypeOf(baz)); // true
console.log(Foo.prototype.isPrototypeOf(baz)); // true
console.log(Object.prototype.isPrototypeOf(baz)); // true
```

### Object.prototype.propertyIsEnumerable()

The propertyIsEnumerable() method returns a Boolean indicating whether the specified property is enumerable.

#### Syntax

```
obj.propertyIsEnumerable(prop)
```

##### Parameters

###### prop

The name of the property to test.

##### Return value

A Boolean indicating whether the specified property is enumerable.

#### Description

Every object has a propertyIsEnumerable method. This method can determine whether the specified property in an object can be enumerated by a for...in loop, with the exception of properties inherited through the prototype chain. If the object does not have the specified property, this method returns false.

#### Examples

The following example shows the use of propertyIsEnumerable on objects and arrays:

```js
var o = {};
var a = [];
o.prop = 'is enumerable';
a[0] = 'is enumerable';

o.propertyIsEnumerable('prop');   // returns true
a.propertyIsEnumerable(0);        // returns true
```

### Object.prototype.toLocaleString()

The toLocaleString() method returns a string representing the object. This method is meant to be overridden by derived objects for locale-specific purposes.

#### Syntax

```
obj.toLocaleString();
```

##### Return value

A string representing the object.

#### Description

Object's toLocaleString returns the result of calling toString().

This function is provided to give objects a generic toLocaleString method, even though not all may use it. See the list below.

#### Objects overriding toLocaleString

* Array: Array.prototype.toLocaleString()
* Number: Number.prototype.toLocaleString()
* Date: Date.prototype.toLocaleString()

### Object.prototype.toString()

The toString() method returns a string representing the object.

#### Syntax

```
obj.toString()
```

##### Return value

A string representing the object.

#### Description

Every object has a toString() method that is automatically called when the object is to be represented as a text value or when an object is referred to in a manner in which a string is expected. By default, the toString() method is inherited by every object descended from Object. If this method is not overridden in a custom object, toString() returns "[object type]", where type is the object type. The following code illustrates this:

```js
var o = new Object();
o.toString(); // returns [object Object]
```

#### Examples

##### Overriding the default toString method

You can create a function to be called in place of the default toString() method. The toString() method takes no arguments and should return a string. The toString() method you create can be any value you want, but it will be most useful if it carries information about the object.

```js
function Dog(name, breed, color, sex) {
  this.name = name;
  this.breed = breed;
  this.color = color;
  this.sex = sex;
}

theDog = new Dog('Gabby', 'Lab', 'chocolate', 'female');
```

```js
theDog.toString(); // returns [object Object]
```

The following code creates and assigns dogToString() to override the default toString() method. This function generates a string containing the name, breed, color, and sex of the object, in the form "property = value;".

```js
Dog.prototype.toString = function dogToString() {
  var ret = 'Dog ' + this.name + ' is a ' + this.sex + ' ' + this.color + ' ' + this.breed;
  return ret;
}
```

With the preceding code in place, any time theDog is used in a string context, JavaScript automatically calls the dogToString() function, which returns the following string:

```js
"Dog Gabby is a female chocolate Lab"
```

##### Using toString() to detect object class

toString() can be used with every object and allows you to get its class. To use the Object.prototype.toString() with every object, you need to call Function.prototype.call() or Function.prototype.apply() on it, passing the object you want to inspect as the first parameter called thisArg.

```js
var toString = Object.prototype.toString;

toString.call(new Date);    // [object Date]
toString.call(new String);  // [object String]
toString.call(Math);        // [object Math]
toString.call(undefined);   // [object Undefined]
toString.call(null);        // [object Null]
```

### Object.prototype.valueOf()

The valueOf() method returns the primitive value of the specified object.

#### Syntax

```
object.valueOf()
```

##### Return value

The primitive value of the specified object.

#### Description

JavaScript calls the valueOf method to convert an object to a primitive value. You rarely need to invoke the valueOf method yourself; JavaScript automatically invokes it when encountering an object where a primitive value is expected.

By default, the valueOf method is inherited by every object descended from Object. Every built-in core object overrides this method to return an appropriate value. If an object has no primitive value, valueOf returns the object itself.

You can use valueOf within your own code to convert a built-in object into a primitive value. When you create a custom object, you can override Object.prototype.valueOf() to call a custom method instead of the default Object method.

##### Overriding valueOf for custom objects

You can create a function to be called in place of the default valueOf method. Your function must take no arguments.

Suppose you have an object type myNumberType and you want to create a valueOf method for it. The following code assigns a user-defined function to the object's valueOf method:

```js
myNumberType.prototype.valueOf = function() { return customPrimitiveValue; };
```

With the preceding code in place, any time an object of type myNumberType is used in a context where it is to be represented as a primitive value, JavaScript automatically calls the function defined in the preceding code.

An object's valueOf method is usually invoked by JavaScript, but you can invoke it yourself as follows:

```js
myNumber.valueOf()
```

#### Examples

```js
function myNumberType(n) {
    this.number = n;
}

myNumberType.prototype.valueOf = function() {
    return this.number;
};

myObj = new myNumberType(4);
myObj + 3; // 7
```

## Methods

When altering the behavior of existing Object.prototype methods, consider injecting code by wrapping your extension before or after the existing logic.  For example, this (untested) code will pre-conditionally execute custom logic before the built-in logic or someone else's extension is executed.

When a function is called, the arguments to the call are held in the array-like "variable" arguments. For example, in the call "myFn(a, b, c)", the arguments within myFn's body will contain 3 array elements corresponding to (a, b, c).  When modifying protoypes with hooks, simply pass this & the arguments (the call state) to the current behavior by calling apply() on the function.  This pattern can be used for any prototype, such as Node.prototype, Function.prototype, etc.

```js
var current = Object.prototype.valueOf

// Since my property "-prop-value" is cross-cutting and isn't always
// on the same prototype chain, I want to modify Object.prototype: 

Object.prototype.valueOf = function () {
  if (this.hasOwnProperty('-prop-value')) {
    return this['-prop-value']
  } else {
    // It doesn't look like one of my objects, so let's fall back on 
    // the default behavior by reproducing the current behavior as best we can.
    // The apply behaves like "super" in some other languages.
    // Even though valueOf() doesn't take arguments, some other hook may.
    return current.apply(this,arguments)
  }
}
```

Since JavaScript doesn't exactly have sub-class objects, prototype is a useful workaround to make a “base class” object of certain functions that act as objects. For example:

```js 
var Person = function() {
  this.canTalk = true;
};

Person.prototype.greet = function() {
  if (this.canTalk) {
    console.log('Hi, I am ' + this.name);
  }
};

var Employee = function(name, title) {
  Person.call(this);
  this.name = name;
  this.title = title;
};

Employee.prototype = Object.create(Person.prototype);
Employee.prototype.constructor = Employee;

Employee.prototype.greet = function() {
  if (this.canTalk) {
    console.log('Hi, I am ' + this.name + ', the ' + this.title);
  }
};

var Customer = function(name) {
  Person.call(this);
  this.name = name;
};

Customer.prototype = Object.create(Person.prototype);
Customer.prototype.constructor = Customer;

var Mime = function(name) {
  Person.call(this);
  this.name = name;
  this.canTalk = false;
};

Mime.prototype = Object.create(Person.prototype);
Mime.prototype.constructor = Mime;

var bob = new Employee('Bob', 'Builder');
var joe = new Customer('Joe');
var rg = new Employee('Red Green', 'Handyman');
var mike = new Customer('Mike');
var mime = new Mime('Mime');

bob.greet();
// Hi, I am Bob, the Builder

joe.greet();
// Hi, I am Joe

rg.greet();
// Hi, I am Red Green, the Handyman

mike.greet();
// Hi, I am Mike

mime.greet();
```