---
date: '2017-03-16 05:51 +0100'
published: true
title: JavaScript - Currying and Partial functions
category:
  - JavaScript
---
In JavaScript we can create a new function dynamically by partially applying a set of arguments to that function.

When you find yourself calling the same function and passing mostly the same parameters, then the function is probably a good candidate for this technique. 

The new function will keep the repeated parameters stored (so you don't have to pass them every time) and will use them to pre-fill the full list of arguments that the original function expects. 

```js
// a curried add()
// accepts partial list of arguments
function add(x, y) {
	
    if (typeof y === 'undefined') {
    
    	// partial application	
        return function (y) {
        	return x + y
        }
        
        // full application
        return x + y
        
    }
}


add(3, 4)               // => 7
var add2000 = add(2000) // => function
add2000(10)             // => 2010


```

The next snippet shows an example of a general-purpose function. Borrowing the slice() method from Array.prototype helps us turn arguments into an array and work more conveniently with it. 

```js
function schonfinkelize(fn) {
     var slice = Array.prototype.slice,
        stored_args = slice.call(arguments, 1);
     return function () {
        var new_args = slice.call(arguments),
              args = stored_args.concat(new_args);
        return fn.apply(null, args);
     };
}
// a normal function
function add(x, y) {
     return x + y;
}
// curry a function to get a new function
var newadd = schonfinkelize(add, 5);
newadd(4); // 9
// another option -- call the new function directly
schonfinkelize(add, 6)(7); // 13
```

When schonfinkelize() is called the first time, it stores a private reference to the slice() method (called slice) and also stores the arguments it was called with (into stored_args), only stripping the first, because the first argument is the function being curried.

Then schonfinkelize() returns a new function. When the new function is called, it has access (via the closure) to the already privately stored arguments stored_args and the slice reference. 

The new function has to merge only the old partially applied arguments (stored_args) with the new ones (new_args) and then apply them to the original function fn (also privately available in the closure). 

The transformation function schonfinkelize() is not limited to single parameters or to single-step currying. Here are some more usage examples: 

```js
// a normal function
function add(a, b, c, d, e) {
     return a + b + c + d + e;
}
// works with any number of arguments
schonfinkelize(add, 1, 2, 3)(5, 5); // 16
// two-step currying
var addOne = schonfinkelize(add, 1);
addOne(10, 10, 10, 10); // 41
var addSix = schonfinkelize(addOne, 2, 3);
addSix(5, 5); // 16
```