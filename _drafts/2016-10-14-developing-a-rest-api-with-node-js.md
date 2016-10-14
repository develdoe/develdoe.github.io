---
date: '2016-10-14 11:12 +0200'
published: false
title: Developing A REST API with Node.js
---
This article will cover the directory structure, through the small design decisions made during development, and finally to the code itself.

This article will display the entire source code for the example API shown in the article [Planning a REST API]('http://develdoe.com/2016/planning-a-rest-api/'). 

We’ll go over the relevant parts. Sadly, some bits and pieces are just plain boring (like the JSON Schema definitions and the simpler models), so I’ll skip it. These things should be pretty self-explanatory to developers anyway, no matter the level of expertise. I’ll cover the development stage as follows:

* Minor simplifications/design decisions made during development.
* Folder structure, it’s important to understand where everything is and why.
* The code itself, file by file, including explanation when needed.

## Minor Changes

We spent two whole articles going over different modules and planning the entire process to develop the API, and yet the plan changes.

There is no escape from minor changes at this stage, unless you spend a lot more time in your design phase.


We'll just keep the employees’ records unaware of their assigned store, simplifying the store employee relationship and We’ll work under the premise that we’re not really making a public API.

That means that we will not require every client to request an access token. Instead, we share a secret passphrase; so clients will always send the MAC code encrypted using this passphrase, and the API will rehash each request to make sure both results match. This way we’re still validating the requests and we remain true to REST. 

We'll Add Swagger UI to test our API. You need [download](https://github.com/swagger-api/swagger-ui) it from and add it into your project. Because the Swagger UI has no de facto support for our authentication scheme. We can send a fixed api_key parameter, but we would have to change the code of the client to get it to use the same algorithm we’re using. This is why we’ve added a small backdoor in our code to let the Swagger UI go by without needing to authenticate each request.

The hack is very simple. Since the UI can send a fixed api_key, we’ll let all requests that have an api_key equal to 777 pass, automatically trusting them. This backdoor will need to be removed when going into production.

We'll use the MVC pattern, this means we'll be having the following elements in out project:

* **Controllers**: Handles requests and calls upon the models for further action.
* **Models**: Holds the main logic of the API. Since in our simple case that logic is basically querying the database, these will be the models used by Mongoose. This will simplify our architecture. Also, Mongoose provides different mechanisms to add extra behaviors to our models (things like setting instance methods or post-action hooks).
* **View**: The view will be embedded inside the model’s code in the form of a method that translates the specifics of one model into a HAL + JSON that can be returned back to the client.

## Folder Structure

![folder_Struct.png]({{site.baseurl}}/img/posts/folder_Struct.png)

* **controllers**: This folder contains the code for our controllers. It also has an index.js file to handle exporting the contents of the rest of them. There is also a base controller here, which contains all the generic methods that all controllers should have; so every new controller can extend this and inherit said methods.
* **lib**: This folder contains the miscellaneous code not big enough to have its own folder, but required across several different places in our project; for instance, database access, helper functions, the config files, and so forth.
* **models**: Inside this folder are the model files. Normally when working with Mongoose, a model’s file has the schema definition, and you return the instantiation of that schema as your model. In our case, the actual definition is somewhere else, so this code handles loading that external definition, adding the extra behavior specific to each model, and then returning it.
* **request_schemas**: Inside this folder are the JSON Schemas used to validate the different requests.
* **schemas**: These are the JSON Schemas of the models, used for the Swagger module to define the UI for testing and for the Mongoose model’s definition. We will have to add some code to translate from the first one to the latter, since they don’t use the same format.
* **swagger-ui**: This folder contains the contents of the Swagger UI project. We’ll need to do some minor adjustments to the index.html file to make it work as we expect it.


## Source Code

Here I’ll list the entire code for the project, including some basic description of the code if required. I’ll go folder by folder.

### controllers

/controllers/index.js
```javascript
module.exports = {
           BookSales: require("./booksales"),
           Stores: require("./stores"),
           Employees: require("./employees"),
           ClientReviews: require("./clientreviews"),
           Clients: require("./clients"),
           Books: require("./books"),
           Authors: require("./authors")
}
```

This file is used to export each controller. Using this technique lets us import the entire folder as a module: `var controllers = require("/controllers")`

/controllers/basecontroller.js
```javascript
var
    _ =         require("underscore"),
    restify =   require("restify"),
    colors =    require("colors"),
    halson =    require("halson")

function BaseController(){
    this.actions = []
    this.server = null
}

BaseController.prototype.setUpActions = function(app,sw){
    this.server = app
    _.each(this.actions, function(act){
        var method = act['spec']['method']
        console.log("setting up auto-doc for (", method, ") - ", act['spec']['nickname'])
        sw['add' + method](act)
        app[method.toLowerCase()](act['spec']['path'], act['action'])
    })
}

BaseController.prototype.addAction = function(spec, fn){
    var newAct = {
        'spec': spec,
        action: fn
    }
    this.actions.push(newAct)
}

BaseController.prototype.RESTError = function(type, msg){
    if(restify[type]){
        return new restify[type](msg.toString())
    } else {
        console.log("Type " + type + " of error not found".red)
    }
}


// Takes care of calling the "toHAL" method on every resource before writing it
// back to the clients
BaseController.prototype.writeHAL = function(res, obj){
    if(Array.isArray(obj)){
        var newArr = []
        _.each(obj, function(item, k){
            item = item.toHAL()
            newArr.push(item)
        })
        obj = halson (newArr)
    } else {
        if(obj && obj.toHAL) obj = obj.toHAL()
        if(!obj)obj = {}
        res.json(obj)
    }
}

module.exports = BaseController
```