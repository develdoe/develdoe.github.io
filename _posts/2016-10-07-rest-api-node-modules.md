---
date: '2016-10-07 06:28 +0200'
published: true
title: REST API Node Modules
category:
  - Node
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

|||
|:-------|---------------------------------------:|
|Category|Request/Response handler, Routes handler, Middleware|
|Description|Express is a full-fledged web framework providing small and robust tools for HTTP servers, making it a great candidate for all kinds of web applications, including RESTful APIs.|
|Home page URL|http://expressjs.com|
|Installation| `npm install –g express-generator`|

##### Code Examples

Express.js is sometimes considered the de facto solution when it comes to building a web application in Node.js. That being said, it doesn’t mean Express.js should be the only choice or that it is right choice for every project.

In version 4 it provides a generator. To initialize the entire project, you have to use the following line of code:

`$express ./express-test`

The framework generates a lot of folders and files, but in general, it’s the structure for a generic web application, one that has views, styles, JavaScript files, and other web app–related resources. This is not for us since we’re building a RESTful API. You’ll want to remove those folders (views and public, more specifically).

To finalize the process, just enter the folder and install the dependencies; this will leave you with a working web application. Check the app.js file if you’re curious about what it takes to initialize the framework.

Let’s now take a look at what it takes to set up a route in Express.js:

```javascript
//...
var app = express()
//...
app.get('/', function(req, res) {
  console.log("Hello world!")
})
```

All that you need to remember when setting up a route is the following: app.VERB(URL-TEMPLATE, HANDLER-FUNCTION). The handler function will receive three parameters: the request object, the response object, and the next function. The last parameter is only useful when you set up more than one handler for the same route and method combination, that way you can chain the methods like they are middleware.

Take a look at the following example:

```javascript
app.route('/users/:id')
   .all(checkAuthentication)
   .all(loadUSerData)
   .get(returnDataHandler)
   .put(updateUserHandler)
```

In the preceding code, there are several interesting things happening:

* A named parameter is used for the ID of the user.
* Two middleware functions are set up for every verb hitting the '/users/:id' route.
* It’s setting up a handler for the GET method hitting the URL, and at the same time, it’s setting up a handler for when the verb is PUT—all in the same line of code.

Express provides its own flavor of named parameters, but there are other things you can do. For instance, you can use regular expressions:

```javascript
router.get(/^\/commit\/(\w+)(?:\.\.(\w+))?$/, function(req, res){
  var from = req.params[0];
  var to = req.params[1] || 'HEAD';
  res.send('commit range ' + from + '..' + to);
});
```

The preceding code matches both '/commit/5bc2ab' and '/commit/5bc2ab..57ba31', and you can see that getting the parameter inside the handler’s code is simple too.
You can also set a callback function to do some processing when a specific named parameter is received:

```javascript
var router = express.Router()

router.param('user_id', function(req, res, next, id) {
   loadUser(id, function(err, usr) {
      if(err) {
         next(new Error("There was an error loading the user's information")) //this will
call erorr handler
      } else {
         req.user = usr
         next()
      }
  })
})

//then on the route definition

app.get('/users/:user_id', function(req, res) {
    //req.user is already defined and can be used
})
```

If there is an error on the user_id callback function, then the route’s handler will never be called, because the first error handler will be called instead.

I already covered the basics for this type of function earlier, but you never saw how to use it with Express.js. You can do it in two ways: set up a global middleware or a route-specific one.
For a global middleware:

```javascript
app.use(function(req, res, next) {
   //your code here will be executed on every request
   next() //remember to call next unless you want the chain to end here.
})
```

For a route-specific middleware, you do this:

```javascript
app.use('/books', function(req, res, next){
        //this function will only be called on this path
        next() //always important to call next unless you don't want the process' flow to
continue.
})
```

You can even set up a route-specific stack of middleware, just by doing this:

```javascript
app.use('/books', function(req, res, next){
//this function will only be called on this path
next() //always important to call next unless you don't want the process' flow to continue.
}, function(req, res, next) {
    //as long as you keep calling next, the framework will keep advancing in the chain until
reaching the actual handler
     next()
})
```


#### Restify


|||
|:-------|---------------------------------------:|
|Category|Request/Response handler, Routes handler, Middleware|
|Description|Restify is a framework specifically design for building REST APIs. It borrows heavily from Express.js (specifically, versions prior to 4.0) because Express is considered the standard when it comes to building web apps.|
|Home page URL|http://mcavage.me/node-restify/|
|Installation| `npm install restify`|

##### Code Examples

Restify borrows a lot of its features from Express, so I’ll focus on the things that it adds.

Initialization is simpler than with Express. The following code is all you need to start up a server:

```javascript
var restify = require('restify');
var server = restify.createServer({
   name: 'MyApp',
});
server.listen(8080);
```

The createServer method provides some helpful options that will simplify your job in the future. Table 5-5 lists some of Restify’s options.

|Option|Description|
|:-|-:|
|certificate|For building HTTPS servers, pass in the path to the certificate here.|
|key|For building HTTPS servers, pass in the path to the key file here.|
|log|Optionally, you can pass in an instance of a logger. It needs to be an instance of node-bunyan.2|
|name|The name of the API. Used to set the server response header; by default it is “restify”.|
|version|A default version for all routes.|
|formatters|A group of content formatters used for content-negotiation.|

In the most basic ways, routes are handled just like Express, you can either pass in the path template and the route handler, or you can pass in a regular expression and the handler.
In a more advanced way, Restify provides some goodies that Express doesn’t. The following subsections provide some examples.

##### Naming Routes

You can set up names for specific routes, which will in turn, allow you to jump from one handler to others using that attribute. Let’s look at how to set up the names first:

```javascript
server.get('/foo/:id', function (req, res, next) { next('foo2');
});
server.get({
    name: 'foo2',
    path: '/foo/:id'
}, function (req, res, next) {
   res.send(200);
   next();
});
```

This code is setting up two different handlers for the same path, but Restify will only execute the first handler it finds, so the second one will never get executed unless the next statement is called with the name of the second route.

Naming is also used to reference routes when rendering the response, which allows for an interesting feature, hypermedia on the response. To be honest, the solution proposed by Restify is a bit basic and it doesn’t really provide a good mechanism for automatically adding hypermedia for self-discovery, but it is more than most other frameworks do. Here is how it works:

```javascript
var restify = require("restify")

var server = restify.createServer()
server.get({
    name: 'country-cities',
    path: '/country/:id/cities'
}, function(req, res, next) {
      res.send('cities')
})
server.get('/country/:id', function(req, res, next) {
  res.send({
    name: "Uruguay",
    cities: server.router.render('country-cities', {id: "uruguay"})
    })
})

server.listen(3000)
```

##### Versioning Routes

Restify provides support for a global version number, as you saw earlier, but it also provides the ability to have different versions on a per-route basis. And, it also provides support for the Accept-version header to pick the right route.

**If the header is missing, and more than one version for the same route is available, Restify will pick the first one defined in the code.**

```javascript
function respV1(req, res, next) {
  res.send("This is version 1.0.2")
}
function respV2(req, res, next) {
  res.send("This is version 2.1.3")
}
var myPath = "/my/route"
server.get({path: myPath, version: "1.0.2"},  respV1)
server.get({path: myPath, version: "2.1.3"},  respV2)
```

Now, when hitting the path with different values for Accept-version, the information in the table below is what you get:

|Version Used|Response|Description|
|:-|-|-:|
||This is version 1.0.2|No version was used, so by default, the server is picking the first one defined.|
|~1|This is version 1.0.2|Version 1.x.x was selected, so that is what the server responds with.|
|~3|`{"code": "InvalidVersion","message": "GET /my/route supports versions: 1.0.2, 2.1.3"}`|An error message is returned when an unsupported version is requested.|

##### Content Negotiation

Another interesting feature that Restify provides is support for content negotiation. All you need to do to implement this feature is provide the right content formatters during initialization, like this:

```javascript
restify.createServer({
  formatters: {
    'application/foo; q=0.9': function formatFoo(req, res, body) {
      if (body instanceof Error)
        return body.stack;
      if (Buffer.isBuffer(body))
        return body.toString('base64');
      return util.inspect(body);
    }
  }
})
```

*By default, Restify comes bundled with formatters for application/json, text/plain, and application/ octect-stream.*

#### Vatican.js

|||
|:-|-:|
|Category|Request/Response handler, Middleware, Routes handling|
|Description|Vatican.js is another attempt of a framework designed to create RESTful APIs. It doesn’t follow the Express/Restify path. Its focus is more on the MVP stage of the API, but it provides an interesting alternative.|
|Home page URL|http://www.vaticanjs.info|
|Installation|`npm install –g vatican`|

##### Code Examples

After installation, Vatican.js provides a command-line script to create the project and add resources and resource handlers to it. So to get the project started:

```bash
$ vatican new test_project
```

The main file (index.js) has the following content:

```javascript
var Vatican = require("vatican")

//Use all default settings
var app = new Vatican()
app.dbStart(function() {
    console.log("Db connection stablished...")
    //Start the server
    app.start()
})
```

Vatican comes with MongoDB integration, so the dbStart method is actually a reference to the connection to the NoSQL storage. By default, the server is assumed to be in localhost and the database name used is vatican-project.

The default port for Vatican is 8753, but just like all defaults in Vatican, it can be overwritten during the instantiation stage. These are the options that can be passed in to the constructor, as shown in the table below:

|Option|Description|
|port|Port of the HTTP server.|
|handlers|Path to the folder where all handlers are stored. By default it’s ./handlers.|
|db|Object with two attributes: host and dbname.|
|cors|This is either a Boolean indicating whether CORS is supported by the API, or an object indicating each of the supported headers.|

Setting up a route in Vatican is also a bit different than the others, the command-line script provides the ability to autogenerate the code for the entity/model file and the controller/handler file, which also includes basic code for the CRUD operations. To autogenerate the code:

```bash
$ vatican g Books -a title:string description:string copies:int -m newBook:post listBooks:get removeBook:delete
```

It basically means that Vatican creates both the handler file and the entity. If you check the handler’s file, you’ll notice how all the actions already have their code. That’s because Vatican was able to guess the meaning of the actions provided in the command line by using their name:

* newBook: Using “new” assumes you’re creating a new instance of the resource.
* listBooks: Using “list” assumes you want to generate a list of items.
* removeBook: Using “remove” assumes you’re trying to remove a resource.

Variations of those words are also valid, and Vatican will use them to guess the code. You can now go ahead and start the server, the endpoints will work and save information to the database.

One final comment on resource generation is about routing. Vatican auto creates them for you. Inside the handler file, you’ll notice annotations in the form of the following:

```javascript
@endpoint (url: /books method: post)
BooksHdlr.prototype.newBook = function(req, res, next) {
var data = req.params.body
//...maybe do validation here?
this.model.create(data, function(err, obj) {
    if(err) return next(err)
    res.send(obj)
})
}
```

The annotation above the method’s definition is not standard JavaScript, but Vatican is able to parse it and turn it into data during boot up. That means that with Vatican there is no routes file; each route is defined above its associated method, and if you want to get a full list of routes for your system, you can use the following command line:

```bash
$ vatican list
```

**The annotations can be commented out with a single line to avoid your editor/linter from complaining about the construct; even then, Vatican.js will be able to parse it.**

Finally, Vatican also fits inside the middleware category, and that’s because even though it’s not based on Connect or Express, it does support Connect-based middleware. The only difference is the method name that uses it.

```
vatican.preprocess(middlewareFunction) //generic middleware for all routes
vatican.preprocess(middelwareFunction, ['login', 'authentication']) //middleware for two
routes: login and authentication.
```

To set the name of a route, you can add that parameter in the annotation, like this:

```javascript
@endpoint(url: /path method: get name: login)
```


#### swagger-node-express

|||
|:-|-:|
|Category|Up-to-date documentation|
|Description|This is a module for Express. It integrates into an Express app and provides the functionalities that Swagger does for documenting APIs, which is a web interface with documentation of each method and the ability to try these methods.|
|Home page URL|https://github.com/swagger-api/swagger-node-express|
|Installation|`npm install swagger-node-express`|

##### Code Examples

The first thing you need to do after you install the module is integrate Swagger into your Express app:

```javascript
// Load module dependencies.
var express = require("express")
, app = express()
, swagger = require("swagger-node-express").createNew(app);
// Create the application.
app.use(express.json());
app.use(express.urlencoded());
```

After integration is done, the next thing to do is add the models and the handlers. The models are in the form of JSON data, where this is defined is left to the preference of the developer. The handlers contain the actual code of the route handlers, along with other descriptive fields that act as documentation. Let’s look at an example of a model definition:

```javascript
exports.models = {
       "Book": {
               "id": "Book",
               "required": ["title", "isbn"],
               "properties": {
                            "title": {
         							"type": "string",
         							"description": "The title of the book"
							}, "isbn": {
                                	"type": "string",
									"description": "International Standard Book Number"
							},
                            "copies": {
         							"type": "integer",
         							"format": "int64",
         							"description": "Number of copies of the book owned
                                    }
                            }
               }
}
```

As you can see, the format used is JSON Schema and it might be tedious to maintain, but it provides a standard way for Swagger to understand how our models are created.

**Manually maintaining a lot of model descriptions might be too much work, and it’s prone to generate errors in the documentation, so it might be a good idea to either use the description to autogenerate the code of the model, or autogenerate the description from the model’s code.**

Once the model description is done, you add it to Swagger like this:

```javascript
// Load module dependencies.
var express = require("express")
, swagger = require("swagger-node-express")
, models = require('./models-definitions').models //....
swagger.addModels(models)
```

Now you move on to the handler’s description, which contains fields describing each method, and the actual code to execute.

```javascript
//Book handler's file
exports.listBooks = {
"spec": {
by title or isbn", "string")]
              },
              "action": function(req, res) {
                          //...
}
}

//main file's code
var bookHandler = require("./bookHandler")

//...
swagger.addGet(bookHandler.listBooks) // adds the handler for the list action and the actual
action itself
```

This code shows how to describe a specific service (a list of books). Again, some of these parameters can be autogenerated, otherwise, manually maintaining a lot of specs can lead to outdated documentation.

Finally, set up the URLs for the Swagger UI (which will display the documentation and will also provide the UI to test the API) and the version:

```javascript
swagger.configure("http://myserver.com", "0.1")
```

Let’s now look at a complete example of a main file, showing the setup and configuration of Swagger
and the Swagger UI.

```javascript
/ Load module dependencies.
var express = require("express")
, models = require("./models").models
, app = express()
, booksHandler = require("./booksHandler") //load the handler's definition
, swagger = require("swagger-node-express").createNew(app) //bundle the app to swagger
// Create the application.
app.use(express.json());
app.use(express.urlencoded());
var static_url = express.static(__dirname + '/ui') //the swagger-ui is inside the "ui"
folder
swagger.configureSwaggerPaths("", "api-docs", "") //you remove the {format} part of the
paths, to simplify things
app.get(/^\/docs(\/.*)?$/ , function(req, res, next) {
    if(req.url === '/docs') {
        res.writeHead(302, {location: req.url + "/"})
        res.end()
        return
}
    req.url = req.url.substr('/docs'.length)
    return static_url(req, res, next)
})
//add the models and the handler
swagger
    .addModels(models)
    .addGet(booksHandler.listBooks)
swagger.configure("http://localhost:3000", "1.0.0")
app.listen("3000")
```


#### I/ODocs

|||
|:-|-:|
|Category|Up-to-date documentation|
|Description|I/O Docs is a live documentation system designed for RESTful APIs. By defining the API using the JSON Schema, I/O Docs generates a web interface to try out the API.|
|Home page URL|https://github.com/mashery/iodocs|
|Installation|`$ git clonehttp://github.com/mashery/iodocs.git $ cd iodocs $ npm install`|

##### Code Examples

After installation is done, the only thing left to do to test the application is create a configuration file; there is a config.json.sample file you can use as a starting point.
To start up the documentation server, use one of the following commands:

```bash
$ npm start       #for *nix and OSX systems
C:\your-project-folder> npm startwin        #for Windows systems
```

After that, use your browser to go to http://localhost:3000 to start testing the documentation system.

If you want to set up your own API, there are a few things to do:

* Add your API to the list of documented APIs inside public/data/apiconfig.json like this:

```javascript
{
"klout": {
        "name": "Klout v2 API"
    },
    "egnyte": {
        "name": "Egnyte API"
    },
    "usatoday": {
        "name": "USA TODAY Census API"
    },
    "foursquare": {
        "name": "Foursquare (OAuth 2.0 Auth Code)"
}, "rdio": {
        "name": "Rdio Beta (OAuth 2.0 Client Credentials)"
    },
 	"rdio2": {
        "name": "Rdio Beta (OAuth 2.0 Implicit Grant)"
    },
    "requestbin": {
        "name": "Requestb.in"
    },
    "bookstore": {
        "name": "Dummy Bookstore API"
    }
}
```

* Create a new file called bookstore.json and store it inside the public/data folder. This new JSON file will contain the description of your API and the methods in it; something like this:

```javascript
{
    "name": "Dummy Bookstore API",
    "description": "Simple bookstore API",
    "protocol": "rest",
    "basePath": "http://api.mybookstore.com",
    "publicPath": "/v1",
    "auth": {
        "key": {
               "param": "key"
} },
    "headers": {
        "Accept": "application/json",
        "Foo": "bar"
    },
    "resources": {
        "Books": {
          "methods": {
             "listBooks": {
                "name": "List of books",
                "path": "/books",
                "httpMethod": "GET",
                "description": "Returns the list of books in stock",
                "parameters": {
                   "sortBy": {
                        "type": "string",
                        "required": false,
                        "default": "title",
                        "description": "Sort the results by title or ISBN code"
} }
             },
                "showBook": {
                   "name": "Show book",
                   "path": "/books/{bookId}",
                   "httpMethod": "GET",
                   "description": "Returns the data of one specific book",
				   "parameters": {
                  	"bookId": {
                      "type": "string",
                      "required": true,
                      "default": "",
                      "description": "The ID of the specific book"
			} }
		} }
	} }
}
```

* Start up the documentation server and point your web browser to it.

Unlike with Swagger, this documentation system is not meant to be integrated into your project, so autogenerating the JSON code might be a bit more difficult.

#### Halson

|||
|:-|-:|
|Category|Hypermedia on the response|
|Description|Halson is a module that helps create HAL-compliant JSON objects, which you’ll then be able to use as part of the response in your API.|
|Home page URL|http://github.com/seznam/halson|
|Installation|`npm install halson`|

##### Code Examples

The API provided by this module is quite straightforward, and if you’ve read about the standard, you should have no problem figuring out how to use it. Here is the example from the readme:

```javascript
var halson = require('halson');
var embed = halson({
        title: "joyent / node",
        description: "evented I/O for v8 javascript"
    })
    .addLink('self', '/joyent/node')
    .addLink('author', {
        href: '/joyent',
        title: 'Joyent'
    });
var resource = halson({
        title: "Juraj Hájovský",
        username: "hajovsky",
        emails: [
             "juraj.hajovsky@example.com",
             "hajovsky@example.com"
        ]
    })
    .addLink('self', '/hajovsky')
    .addEmbed('starred', embed);
console.log(JSON.stringify(resource));
```

All you need to know is how to add links and what an embedded object is.


#### HAL

|||
|:-|-:|
|Category|Hypermedia on the response|
|Description|HAL is an alternative to HALSON. It provides a simpler interface but the same underlying functionality: abstracting the HAL+JSON format and giving the developer an easy way to use it.|
|Home page URL|https://www.npmjs.com/package/hal|
|Installation|`npm install hal`|

##### Code Examples

The API of this module is simpler than the one provided by HALSON and it also provides XML encoding (remember that even though you’re not focusing on XML, it can be a possible second representation for your resources).

Let’s look at a simple example:

```javascript
var hal = require('hal');
var books = new hal.Resource({name: "Books list"}, "/books")
var listOfBooks = [
  new hal.Resource({id: 1, title: "Harry Potter and the Philosopher's stone", copies: 3}, "/
books/1"),
  new hal.Resource({id: 2, title: "Harry Potter and the Chamber of Secrets", copies: 5}, "/
books/2"),
  new hal.Resource({id: 3, title: "Harry Potter and the Prisoner of Azkaban", copies: 6}, "/
books/3"),
  new hal.Resource({id: 4, title: "Harry Potter and the Goblet of Fire", copies: 1}, "/
books/4"),
  new hal.Resource({id: 5, title: "Harry Potter and the Order of the Phoenix", copies: 8},
"/books/5"),
  new hal.Resource({id: 6, title: "Harry Potter and the Half-blood Prince", copies: 2}, "/
books/6"),
  new hal.Resource({id: 7, title: "Harry Potter and the Deathly Hollows", copies: 7},"/
books/7")
]
books.embed('books', listOfBooks)
console.log(JSON.stringify(books.toJSON()))
```


#### JSON-Gate

|||
|:-|-:|
|Category|Request/Response validation|
|Description|This module validates the structure and content of a JSON object against a predefined schema that follows the JSON Schema format.|
|Home page URL|https://www.npmjs.com/package/json-gate|
|Installation|`npm install json-gate`|

##### Code Examples

The usage of this module is quite simple. First, you need to define the schema against which your objects will be validated. This can be done directly with the createSchema method or (recommended) in a separate file, and then passed to the validator. After the schema has been added, you can proceed to validate as many objects as you need:

```javascript
var createSchema = require('json-gate').createSchema;
var schema = createSchema({
    type: 'object',
    properties: {
        title: {
               type: 'string',
               minLength: 1,
               maxLength: 64,
               required: true
		}, copies: {
               type: 'integer',
               maximum: 20,
               default: 1
		}, isbn: {
               type: 'integer',
               required: true
        }
    },
    additionalProperties: false
});
var invalidInput = {
    title: "This is a valid long title for a book, it might not be the best choice!",
    copies: "3"
}
try {
   schema.validate(invalidInput);
} catch(err) {
    return console.log(err)
}
```

The preceding code will output the following error:

```bash
[Error: JSON object property 'title': length is 71 when it should be at most 64]
```

There are two things to note here:

* On one hand, the error message is very “human friendly.” All the error messages reported by JSON-Gate are like this, so it’s easy to understand what you did wrong.
* On the other hand, as you probably noticed, the invalidInput object has two errors in its format; the validation stops at the first error, so correcting multiple problems might be slow because you’ll have to correct them one at a time.

If you’re not into catching exceptions (and why should you in Node.js?), there is an alternative to the validate method, which is passing in a second argument—a callback function with two arguments: the error object and the original input object.


#### TV4

|||
|:-|-:|
|Category|Request/Response validation|
|Description|This module provides validation against version 4 of the JSON Schema.|
|Home page URL|https://www.npmjs.com/package/tv4|
|Installation|`npm install tv4`|

##### Code Examples

The main difference between this validator and JSON-Gate is that this one is specific for version 4 of the JSON Schema draft. It also allows you to collect multiple errors during validation and to reference other schemas, so you can reuse parts of the schema in different sections.

Let’s look at some examples:

```javascript
var validator = require("tv4")
var schema ={
    "title": "Example Schema",
    "type": "object",
    "properties": {
        "firstName": {
             "type": "string"
        },
        "lastName": {
             "type": "string"
        },
        "age": {
             "description": "Age in years",
             "type": "integer",
             "minimum": 0
} },
    "required": ["firstName", "lastName"]
}
var invalidInput = {
    firstName: 42,
age: "100"
}
var results = validator.validateMultiple(invalidInput, schema)
console.log(results)
```

The output is much bigger than the one from JSON-Gate and it needs a bit of parsing before being able to use it, but it also provides quite a lot of information aside from the simple error message.


## Summary

This article covered a lot of modules that will help you create the perfect API architecture. You saw at least two modules for every category on options for picking the tools for the job.
