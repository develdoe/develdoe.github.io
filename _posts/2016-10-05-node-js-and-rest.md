---
date: '2016-10-05 06:17 +0200'
published: true
title: Node.js and REST
category:
  - js
---
## Asynchronous Programming

Asynchronous programming means that for every asynchronous function that you execute, you can’t expect it to return the results before moving forward with the program’s flow. Instead, you’ll need to provide a callback block/function that will be executed once the asynchronous code finishes.

![](http://www.techthali.org/wp-content/uploads/2012/07/npm8.png)

Above, in the first image, a instruction run in synchronous manner. In order to execute the instruction, you need to wait as long as the instruction takes. In an asynchronous manner you can provide the instruction as a callback to that, allowing you to execute other instruction without waiting for the first one to execuite. The next instruction is executed right after the first one starts the asynchronous “long time running instruction”.

This is a very simple example of the potential benefits of asynchronous programming. Sadly, like with most in this digital world, nothing comes without a price, and the added benefits also come with a nasty trade-off. Developers are trained to think of their code in the sequential way they write it, so debugging a code that is not sequential can be difficult to newcomers.

For instance, below show a piece of code written in a synchronous and an asynchronous manner, respectively:

**Synchronous:**

```javascript
console.log("About to read the file...")
var content = Fs.readFileSync("/path/to/file") 
console.log("File content: ", content)
```

**Asynchronous Version of a Simple File Read Operation with a Common Mistake:**

```javascript
console.log("About to read the file...")
var content = ""
fs.readFile("/path/to/file", function(err, data) {
   content = data
})
console.log("File content: ", content)
```

The last peace of code does not print the content of the file since the callback is executed after the last line of code.

**Correct Asynchronous File Read Operation:**

```javascript
console.log("About to read the file...")
var content = ""
fs.readFile("/path/to/file", function(err, data) {
   content = data
   console.log("File content: ", content)
})
```

You just moved the last line into the callback function, so you’re sure that the content variable is set correctly.

### Async Advanced

Asynchronous programming is not just about making sure that you set up the callback function correctly, it also allows for some interesting flow control patterns that can be used to improve the efficiency of the app. 

Let’s look at two distinct and very useful control flow patterns for asynchronous programming: parallel flow and serial flow.

#### Parallel Flow

The idea behind parallel flow is that the program can run a set of nonrelated tasks in parallel, but only call the callback function provided (to gather their collective outputs) after all tasks have finished executing.

Parallel Function:

```javascript
//functionX symbols are references to individual functions
parallel([function1, function2, function3, function4], function(data) {
   ///do something with the combined output once they all finished
})
```

In order to know when each of the functions passed in the array have finished execution, they’ll have to execute a callback function with the results of their operation. The callback will be the only attribute they receive. Below shows the parallel function.

Implementation:

```javascript
function parallel(funcs, callback) {
     var results = [],
           callsToCallback = 0
     funcs.forEach(function(fn) { // iterate over all functions
       setTimeout(fn(done), 200) // and call them with a 200 ms delay
      })
    function done(data) { // the functions will call this one when they finish and
they’ll pass the results here
       results.push(data)
       if(++callsToCallback == funcs.length) {
          callback(results)
       }
} }
```

The implementation in above runs a set of functions in a parallel way (since Node.js runs in a single thread, true parallelism is not possible, so this is as close as you can get). This type of control flow is particularly useful when dealing with calls to external services.

Let’s look at a practical example. Assume your API needs to do several operations that, although aren’t related to each other, need to happen before the user can see the results. For instance, load the list of books from the database, query an external service to get news about new books out this week, and log the request into a file. 

If you were to execute all of those tasks in a series, waiting for one to finish before the next one can be run, then the user would most probably suffer a delay on the response because the total time needed for the execution is the sum of all individual times.

But if you can execute all of them in parallel, then the total time is actually equal to the time it takes the slowest task to execute. Let’s look at both cases.

SerialFlow:

```javascript

//request handling code...
//assume "db" is already initialized and provides an interface to the data base db.query("books", {limit:1000, page: 1}, 

function(books) {
 services.bookNews.getThisWeeksNews(function(news) {
     services.logging.logRequest(request, function() { //nothing returned, but you need to call it so you know the logging finished
       response.render({listOfBooks: books, bookNews: news})
    }) 
  })
})
```

Parallel Execution Flow:

```javascript
//request handling code... 
parallel([
  function(callback) { db.query("books", {limit: 1000, page: 1}, callback) }),
  function(callback) { services.bookNews.getThisWeeksNews(callback) }),
  function(callback) { services.logRequest(request, callback) })
 ], function(data) {
     var books = findData(‘books’, data)
     var news = findData(‘news’, data)
     response.render({listOfBooks: books, bookNews: news})
})
```

Above show how each approach looks. The findData function simply looks into the data array, and based on the structure of the items, returns the desired one (first parameter). 

In the implementation of parallel it is needed because you can’t be sure in which order the functions finished and then sent back their results.

Aside from the clear speed boost that the code gets, it’s also easier to read and easier to add new tasks to the parallel flow—just add a new item to the array.

#### Serial Flow

The serial flow provides the means to easily specify a list of functions that need to be executed in a particular order. This solution doesn’t provide a speed boost like parallel flow does, but it does provide the ability to write such code and keep it clean, staying away from what is normally known as spaghetti code.

Below shows what you should try to accomplish:

Signature of the Serial Function:

```javascript
serial([
 function1, function2, function3
], function(data) {
   //do something with the combined results
})
```

Below shows what you shouldn’t do.

CommonCaseofNestedCallbacks:

```javascript
  function1(function(data1) {
    function2(function(data2) {
     function3(function(data3) {
      //do something with all the output
    } 
  }
}
```

You can see how the code in above could get out of hand if the number of functions kept growing. So the serial approach helps keep the code organized and readable.

Let’s look at a possible implementation of the serial function.

Implementation of the Serial Function:

```javascript
function serial(functions, done) {
    var fn = functions.shift() //get the first function off the list
    var results = []
    fn(next)
    function next(result) {
        results.push(result) //save the results to be passed into the final callback once you don’t have any more functions to execute.
        var nextFn = functions.shift()
        if (nextFn) nextFn(next)
        else done(results)
    }
}
```

There are more variations to these functions, like using an error parameter to handle errors automatically, or limiting the number of simultaneous functions in the parallel flow.

All in all, asynchronous programming brings a lot of benefits to implementing APIs. Parallel workflow comes in very handy when dealing with external services, which normally any API would deal with; for instance, database access, other APIs, disk I/O, and so forth. 

And at the same time, the serial workflow is useful when implementing things like Express.js. For a fully functional and tested library that thrives on asynchronous programming, please check out async.js.


## Asynchronous I/O

A specific case of asynchronous programming relates to a very interesting feature provided by Node.js, asynchronous I/O. 

This feature is highly relevant to the internal architecture of Node.js. Node.js doesn’t provide multithreading, it actually works with a single thread that runs an event loop.

![](http://i.stack.imgur.com/BTm1H.png)

In a nutshell, Node.js was designed with the mindset that I/O operations are the actual bottleneck in every operation, not the processing power. So every request received by the node process will work inside the event loop until an I/O operation is found. When that happens, the callback is registered on a separate queue and the main program’s flow continues. Once the I/O operation finishes, the callback is triggered and the code inside it is run.


### Async I/O vs. Sync I/O

Node.js works best using async I/O, I’ve done some very simple benchmarking. I’ve created a simple API with two endpoints:

* /async: This reads a 1.6MB file asynchronously before returning a simple JSON response.
* /sync: This reads a 1.6MB file synchronously before returning a simple JSON response.

Both endpoints do exactly the same, only in a different manner. The idea is to prove that even in such simple code, the event loop can handle multiple requests better when the underlying code makes use of the asynchronous I/O provided by the platform.

Below is the code of both endpoints, the API was written using Vatican.js.

Async handler:

```javascipt
var fs = require("fs")
module.exports = AsyncHdlr;
function AsyncHdlr(_model) { this.model = _model }
//@endpoint (url: /async method: get)
AsyncHdlr.prototype.index = function(req, res, next) {
    fs.readFile(__dirname + "/../file.txt", function (err, content) {
        res.send({
            success: true
    }) 
  })
}
```

Sync handler:

```javascript
var fs = require("fs")
module.exports = SyncHdlr;
function SyncHdlr(_model) { this.model = _model }
//@endpoint (url: /sync method:get)
SyncHdlr.prototype.index = function(req, res, next) {
    var content = fs.readFileSync(__dirname + "/../file.txt")
    res.send({
        success: true
}) }

```

Results from the Benchmark of the Two Endpoints:

|Synchronous Endpoint|Asynchronous Endpoint|
|:-------------------|--------------------:|
|Requests per second: 2411.28 [#/sec.] (mean)|Requests per second: 2960.79 [#/sec.] (mean)|
|Time per request 41.472 [ms] (mean)|Time per request: 33.775 [ms] (mean)|
|Time per request: 0.415 [ms] (mean, across all concurrent requests)|Time per request: 0.338 [ms] (mean, across all concurrent requests)|
|Transfer rate: 214.28 [KBps] received|Transfer rate: 263.12 [KBps] received|

As you can see above, for even the simplest of examples, there are 549 more requests being served by the asynchronous code than in the synchronous code in the same amount of time. Another interesting item is that each request is almost 8 milliseconds faster on the asynchronous endpoint; this might not be a huge number, but considering the nonexistent complexity of the code you’re using, it’s quite relevant.

## Simplicity

Node.js (JavaScript) is not a complicated language. It follows the basic principles that similar scripting languages follow (like Ruby, Python, and PHP). Node.js is simple enough for any developer to pick up and start coding in no time, and yet it’s powerful enough to achieve almost anything developers can set their minds to.

JavaScript has gained a lot of traction over the years, but it has also gained a lot of haters, and they have their very valid reasons, a nonstandard object- oriented model, weird usage of the this keyword, a lack of functionality built into the language (it has a lot of libraries dedicated to implementing basic features that come built-in in other languages), and the list goes on. In the end, every tool needs to be chosen based on its strengths. Node.js is a particularly strong option for developing APIs, as you’re about to see.

Node.js adds a certain useful flavor to the language, simplifying a developer’s life when trying to develop back-end code. It not only adds the required utilities to work with I/O (which front-end JavaScript doesn’t have for obvious security reasons), but it also provides stability for all the different flavors of JavaScript that each web browser supports. 

One example of this is how easy it is to set up a web server with just a few lines of code:

```javascript
var http = require("http")
http.createServer(function(req, res) { //create the server
        //request handler code here
});
http.listen(3000) //start it up on port 3000
```

JavaScript also has the advantage of being the standard front-end language for all commercial web browsers, which means that if you’re a web developer with front-end experience, you have certainly come across JavaScript.


### Dynamic Typing

Dynamic typing is a basic characteristic, present in most common languages nowadays, but it’s not less powerful because of that. This little feature allows the developer to not have to think too much when declaring a variable; just give it a name and move on:

```javascript
var a, b, tmp //declare the variables (just give them names)

//initialize them with different types
a = 10
b = "hello world"

//now swap the values
tmp = a
a = b //even with automatic casting, a language like C won’t be able to cast "hello
world" into an integer value
b = tmp

//print values
console.log(a) //prints "hello world"
console.log(b) //prints 10
```

### Object-Oriented Programming

JavaScript it not an object-oriented language, but it does have support for some of these features. You’ll have enough of them to conceptualize problems and solutions using objects, which is always a very intuitive way of thinking, but at the same time, you're not dealing with concepts like polymorphism, interfaces, or others that despite helping to structure code, have proven to be dispensable when designing applications.

```javascript
var myObject = { //JS object notation helps simplify definitions
        myAttribute: "some value",
        myMethod: function(param1, param2) {
                //does something here
        } 
}
//And the just... 
myObject.myMethod(...)
```

Another example:

```javascript
var aDog = { //behave like a dog
        makeNoise: function() {
                console.log("woof!");
        } 
}
var aCat = { //behave like a cat
        makeNoise: function() {
                console.log("Meewww!");
        } 
}
var myAnimal = { //our main object
        makeNoise: function() {
console.log("cri... cri....") speak: function() {
                this.makeNoise()
        } 
}
myAnimal.speak() //no change, so.. crickets!
myAnimal.speak.apply(aDog) //this will print "woof!"

//switch behavior
myAnimal.speak.apply(aCat) //this will now print "Meewww!"
```
You were able to encapsulate a simple behavior into an object and pass it into another object to automatically overwrite its default one.¨


### Prototypal Inheritance

Linked to the one above, the prototypal inheritance feature allows for incredibly easy extension of your objects at any moment of their life cycle; powerful and simple:

```javascript
var Parent = function() {
        this.parentName = "Parent"
}
var Child = function() {
}
Child.prototype = new Parent()
var childObj = new Child();
console.log(childObj.parentName)

console.log(childObj.sayThanks) //that's undefined so far

Parent.prototype.sayThanks = function() { //you "teach" the method to the parent
        console.log("Thanks!")
}

console.log(childObj.sayThanks()) //the child can say thanks now
```


### Functional Programming Support

JavaScript is not a functional programming language; but then again, it does have support for some of its features, such as having first-class citizen functions, allowing you to pass them around like parameters, and return closures easily. 

This feature makes it possible to work with callbacks, which, as you’ve already seen, is the basis for asynchronous programming. Let’s look at a quick and simple functional programming example:

```javascript
function adder(x) {
  return function(y) {
    return x+y 
  }
}

var add10 = adder(10) //you create a new function that adds 10 to whatever you pass to it. 
console.log(add10(100)) //will output 110
```

A more complex example is an implementation of the map function, which allows you to transform the values of an array by passing the array and the transformation function. Let’s first look at how you’d use the map function.

```javascript
map([1,2,3,4], function(x) { return x * 2 }) //will return [2,4,6, 8]
map(["h","e","l","l","o"], String.prototype.toUpperCase) //will return ["H","E","L","L","O"]
```

Now let’s look at a possible implementation using the functional approach.

```javascript
function reduce(list, fn, init) {
        if(list.length == 0) return init
        var value = list[0]
        init.push(fn.apply(value, [value])) //this will allow us to get both the functions that receive the value as parameters and the methods that use it from it’s context (like toUpperCase)
        return reduce(list.slice(1), fn, init) //iterate over the list using it’s tail
(everything but the first element)
}

function map(list, fn) {
        return reduce(list, fn, [])
}
```


### Duck Typing

The type of a variable is determined by its content and properties, not by a fixed value. So the same variable can change its type during the life cycle of your script. Duck typing is both a very powerful feature and a dangerous feature at the same time:

```javascript
var foo = "bar"
console.log(typeof foo) //will output "string"
foo = 10
console.log(typeof foo) //this will now output "number"
```


### Native Support for JSON

Since JSON actually spawned from JavaScript. Having native support for the main transport language used nowadays is a big plus:

```javascript
var myJSONProfile = {
        "first_name": "Fernando",
        "last_name": "Doglio",
        "current_age": 30,
        "married": true,
        "phone_numbers": [
}

//And you can interact with that JSON without having to parse it or anything
console.log(myJSONProfile.first_name, myJSONProfile.last_name)
```

This particular feature is especially useful in several cases. 

For instance, when working with a document-based storage solution (like MongoDB) because the modeling of data ends up being native in both places (your app and the database). Also, when developing an API, you’ve already seen that the transport language of choice these days is JSON, so the ability to format your responses directly with native notation (you could even just output your entities, for that matter) is a very big plus when it comes to ease of use.

The list could be extended, but those are some pretty powerful features that JavaScript and Node.js bring to the table without asking too much of the developer. They are quite easy to understand and use.

## Node Package Manager

Another point in favor of Node.js is its amazing package manager. As you might know, development in Node is very module dependent, meaning that you’re not going to be developing the entire thing; most likely you’ll be reusing someone else’s code in the form of modules.

This is a very important aspect of Node.js, because this approach allows you to focus on what makes your application unique, and lets the generic code be integrated seamlessly. You don’t have to recode the library for HTTP connectivity, or your route handler on every project (in other words, you don’t have to keep reinventing the wheel). Just set the dependencies of your project into the package.json file, using the best-suited module names, and then npm will take care of going through the whole dependency tree and install everything needed.

The amount of active users and modules available assures you that you’ll find what you need. In the rare occasions when you don’t, you can contribute by uploading that specific module to the registry and help the next developer that comes looking for it.

To start using npm, just visit their site at www.npmjs.org. There you’ll see a list of recently updated packages to get you started, and some of the most popular ones as well.

If want to install it directly, just write the following line into your Linux console:

```bash
$ curl https://www.npmjs.org/install.sh | sh
```

You need to have Node.js installed to use it properly. You can begin installing modules by simply typing:

```bash
$ npm install [MODULE_NAME]
```

This command downloads the specified module into a local folder called node_modules; so try to run it from within your project’s folder.

You can also use npm to develop your own modules and publish them into the site by using the following:

```bash
$ npm publish
```

The preceding command takes attributes from the package.json file, package the module, and upload everything into npm’s registry.


## Summary

This article covered the advantages of Node.js for the common developer, especially how its features improve the performance of I/O–heavy systems such as APIs.
