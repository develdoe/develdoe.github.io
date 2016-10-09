---
date: '2016-10-07 06:28 +0200'
published: false
title: REST API Node Modules
category:
  - node
---
Node modules you will need when building a REST API with node:

* **HTTP request and response handling:** This is the most basic feature. As you’re about to see, there are plenty of options out there to pick from.
* **Routes handling:** Aside from the preceding, request handling is one of the most important and crucial parts of our system.
* **Pre-processing chain (middleware):** You can leave out post-processing because it’s a less common feature, but pre-processing (or middleware) is common and very useful.
* **Up-to-date documentation:** It so happens that there is a module that will help here, so you might as well add it.
* **Hypermedia on the response:** Part of a REST, so you’ll add it using the HAL standard.
* **Response and request format validation:** Finally, this will be an added bonus; as a good practice, always validate the format of the requests and responses.

## Our Alternatives

Instead of looking at each point individually, you’ll take a look at each of the modules, and I’ll evaluate them accordingly. Some of them, as you’ll see, handle more than just one thing, which sometimes comes in handy because getting unrelated modules to work together is not always an easy task.

### Request/Response Handling

Regarding request and response handling, they usually both come in the same module. They are the basics of every HTTP application you intend to make. If you don’t know how to handle the HTTP protocol, you can’t move forward. And because Node.js is such a good fit for HTTP applications, there are quite a few modules that will help you in this task. You need something that can do the following:

* Listen on a specific port for HTTP traffic.
* Translate the HTTP message into a JavaScript object so that you can read it and use it without having to worry about parsing it or about any of the details of the HTTP protocol.
* Write an HTTP response without having to worry about the HTTP message format.

Writing code that listens in a specific port for HTTP traffic is simple, actually, Node.js provides all the tools you need, out of the box, to achieve the three preceding points. Then why do we need extra modules if we can easily do it ourselves? That’s a very valid question, and to be honest, it all depends on your needs. If the system you’re building is small enough, then it might be a good idea to build the HTTP server yourself; otherwise, it’s always good to use a well-tested and tried module. They also solve other related issues for you, such as routes handling, so going with a third-party module might be a good choice.

### Routes Handling

Routes handling is tightly coupled with request and response handling. It’s the next step in the processing of the request. Once you translate the HTTP message into an actual JavaScript object that you can work with, you need to know which piece of code needs to handle it. This is where routes handling comes in.

There are two sides to this part. First, you need to be able to set up the routes in your code, and associate the handler’s code with one or more specific routes. And then the system needs to grab the route of the requested resource and match it to one of yours. That might not be such an easy task. Remember that most routes in any complex system have parameterized parts for things like unique IDs and other parameters. For example, take a look at the table below:

|This|needs to match this|
|:------|-----------------------:|
|/v1/books/1234412|/v1/books/:id|
|/v1/authors/jkrowling/books|/v1/:author_name/books|

Usually, routing frameworks provide some sort of templating language that allows developers to set up named parameters in the route template. Later the framework will match the requested URLs to the templates, taking into consideration those variable parts added. Different frameworks add different variations of this.


### Middleware

This is the name that the pre-processing chain normally gets in the Node.js world, and that is because the Connect framework (which is the framework most other web frameworks are based on) has this functionality. I already talked about this topic in the previous chapter, so let’s look at some examples of middleware functions that are compatible with Connect-based frameworks:

```javascript
//Logs every request into the standard output
function logRequests(req, res, next) {
   console.log("[", req.method, "]", req.url)
   next() 
}
````
```javascript
//Makes sure that the body of the request is a valid json object, otherwise, 
//it throws an error
function formatRequestBody(req, res, next)  {
    if(typeof req.body == 'string') {
      try {
        req.body = JSON.parse(req.body)
     } catch (ex) {
       next("invalid data format")
     }
}
next() 
}
```

Both examples are different, but at the same time they share a common function signature. Every middleware function receives three parameters: the request object, the response object, and the next function. 

The most interesting bit here is the last parameter, the next function, calling it is mandatory unless you want to end the processing chain right there. It calls the next middleware in the chain, unless you pass in a value, in which case it’ll call the first error handler it finds and it’ll pass it the parameter (normally an error message or object).

The use of middleware is very common for things like authentication, logging, session handling, and so forth.


### Up-to-Date Documentation

Keeping up-to-date documentation of the API’s interface is crucial if you want developers to use your system. I’ll go over some modules that will help in that area. Some of the modules add more overhead than others, but the main goal is to have some sort of system that updates its documentation as automatically as possible.


### Hypermedia on the Response

If you want to follow the REST style to the letter, you need to work this into your system. It is one of the most forgotten features of REST—and a great one, since it allows for self-discovery, another characteristic of a RESTful system.

For this particular case, you’ll go with a pre-defined standard called HAL, so you’ll be checking out some modules that allow you to work with this particular format.


### Response and Request validation

I’ll also go over some modules that will let you validate both the response and the request format. Our API will work with JSON alone, but it’s always useful to validate the structure of that JSON in the request due to errors in the client application, and in the response to ensure that there are no errors in the server side after code changes.

Adding a validation on every request might be too big of an overhead, so an alternative might be a test suite that takes care of doing the validation when executed. But the request format validation will have to be done on every request to ensure that the execution of your system is not tainted by an invalid request.

### The List of Modules

Now let’s go over some modules that take care of one or several of the categories mentioned

We won’t compare them because it’s not an easy thing to do considering that some modules only handle one thing, whereas others take care of several things. So after going over then, I’ll propose a combination of these modules, but you will have enough information to pick a different combo if it fits your problem better.

#### HAPI

HAPI Module Information:

|||
|:-------|---------------------------------------:|
|Category|Request/Response handler, Routes handler|
|Description|HAPI is a configuration-centric web framework designed to create any kind of web application, including APIs. The main goal of HAPI is to allow developers to focus on coding the logic of an application, leaving infrastructure code to the framework.|
|Home page URL|http://hapijs.com/|
|Installation| `npm install hapi`|

##### Code Examples

After installation, the most basic thing you can do to initialize the system:

```javascript
var hapi = require("hapi")

var server = new hapi.Server()
server.connection({ port: 3000 })
server.start(function() {
  console.log("Server running at: ", server.info.url)
})
```
As you can see, this example is quite basic, but the steps required to initialize the application are there. The server.connection line returns a server object with the new connection selected. That means you could maintain several open connections at the same time, like this:

```javascript
var Hapi = require('hapi');

var server = new Hapi.Server()

// Create the API server with a host and port
var apiServer = server.connection({
    host: 'localhost',
    port: 8000
})

//Get list of books for normal client app
apiServer.route({
    method: 'GET',
    path: '/books',
    handler: function(req, reply) {
        //... code to handle this route
    }
})

// Create the Admin  server with a port
var adminServer = server.connection({
port: 3000 })

//Setup route to get full list of users for admin (with with credential information)
adminSever.route({
    method: 'GET',
    path: '/users',
    handler: function(req, reply) {
        //check credentials...
        //get full list of users...
        //reply(list)
    } 
})

server.start(function() {
 console.log("Server started!")
})
```

This code initializes the application, which in turn sets up two different servers: one for the API itself and another one for an admin system. In this code you can also see how easy it is to set up routes with HAPI. Although the code can clearly be cleaned up and the routes definitions can be taken out to a separate file, this is a great example of how two (or more!) servers with their respective routes can be configured using this framework.

Another interesting bit that HAPI provides is the route templates you can use by setting up your own. With it, you can use named parameters, in the following way:

```javascript
var Hapi = require('hapi')
var server = new Hapi.Server()
server.connection({ port: 3000 })

var getAuthor = function (request, reply) {
        // here the author and book parameters are inside
        // request.params
}

server.route({
    path: '/{author}/{book?}',
    method: 'GET',
    handler: getAuthor
})
```

In the preceding code, when setting up the route, anything that’s inside curly brackets is considered a named parameter. The last one, has an ? added to it, which means it’s optional.

**Only the last named parameter can be set as optional.***

In addition to the ? mark, you can use another special character to tell HAPI the number of segments a named parameter should match. That character is the * and it should be followed by a number greater than 1, or nothing, if you want it to match any number of segments.

**Just like the ? mark, only the last parameter can be configured to match any number of segments.**

```javascript
server.route({
    path: '/person/{name*2}', // Matches '/person/john/doe'
    method: 'GET',
    handler: getPerson
})

server.route({
    path: '/author/{name*}', // Matches '/author/j/k/rowling' or '/author/frank/herbert'
or /author/
    method: 'GET',
    handler: getAuthor
})

function getAuthor(req, reply) {
   // The different segments can be obtained like this:
  var segments = req.params.name.split('/')
}
```


#### Express.js
#### Restify
#### Vatican.js
#### swagger-node-express
#### I/ODocs
#### Halson
#### HAL
#### JSON-Gate
#### TV4
## Summary
