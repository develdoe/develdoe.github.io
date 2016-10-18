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

*/controllers/index.js*

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

*/controllers/basecontroller.js*

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

Every controller extends this object, gaining access to the methods shown earlier. We’ll use basic prototypical inheritance, as you’ll see in a bit when we start listing the other controllers’ code. As for this one, let’s quickly go over the methods it exposes:

* **setUpActions**: This method is called upon instantiation of the controller; it is meant to add the actual routes to the HTTP server. This method is called during the initialization sequence for all controllers exported by the index.js file.
* **addAction**: This method defines an action, which consists of the specs for that action and the actual function code. The specs are used by Swagger to create the documentation, but they’re also used by our code to set up the route; so there are bits inside the JSON spec that are also meant for the server, such as the path and method attributes.
* **RESTError**: This is a simple wrapper method around all the error methods provided by Restify. It provides the benefit of cleaner code.
* **writeHAL**: Every model defined (as you’ll see next) has a toHAL method, and the writeHAL methods take care of calling it for every model we’re trying to render. It basically centralizes the logic that deals with collections or simple objects, depending on what we’re trying to render.


*/controllers/books.js*

```javascript
var
    BaseController = require("./basecontroller"),
    _ = require("underscore"),
    swagger = require("swagger-node-restify")

function Books(){}

Books.prototype = new BaseController()

module.exports = function(lib){
    var controller = new Books()

    // Helper function for the POST action
    function mergeStores(list1, list2){
        var stores1 = {}
        var stores2 = {}
        _.each(list1, function(st){ if(st.store) stores1[st.store] = st.copies })
        _.each(list2, function(st){ if(st.store) stores2[st.store] = st.copies })
        var stores = _.extend(stores1, stores2)
        return _.map(stores, function(v,k){ return {store: k, copies: v} })
    }

    controller.addAction({
        'path': '/books',
        'method': 'GET',
        'summary': 'Returns the list of books',
        'params': [
                    swagger.queryParam('q','Search term','string'),
                    swagger.queryParam('genre','Filter by genre','string')],
        'responseClass':'Book',
        'nickname':'getBooks'
    }, function(req,res,next){
        var criteria = {}
        if(req.params.q){
            var expr = new RegExp('.*' + req.param.q + '.*')
            criteria.$or = [
                {title:expr},
                {isbn_code:expr},
                {description:expr}
            ]
        }
        if(req.params.genre){
            criteria.genre = req.params.genre
        }
        lib.db.model('Book')
            .find(criteria)
            .populate('stores.store')
            .exec(function(err,books){
                if(err) return next(err)
                controller.writeHAL(res, books)
            })
    })

    controller.addAction({
        'path': '/books/{id}',
        'method': 'GET',
        'params': [swagger.pathParam('id','The id of the book','int')],
        'summary': 'Returns the full data of a book',
        'nickname': 'getBook',
    }, function(req,res,next){
        var id = req.params.id
        if(id){
            lib.db.model("Book")
                .findOne({id:id})
                .populate('authors')
                .populate('stores')
                .populate('reviews')
                .exec(function(err, book){
                    if(err) return next(controller.RESTError('InternalServerError', err))
                    if(!book) return next(controller.RESTError('ResourceNotFoundError', 'Book not found'))
                    controller.writeHAL(res,book)
                })

        } else { next(controller.RESTError('InvalidArgumentError', 'Missing book id')) }
    })
    controller.addAction({
        'path': '/books',
        'method': 'POST',
        'params': [swagger.bodyParam('book','JSON representation of the new book','string')],
        'summary': 'Adds a new book into the collection',
        'nickname': 'newBook'
    }, function(res,req,next){
        var bookData = req.body
        if(bookData){
            isbn = bookData.isbn_code
            lib.db.model("Book")
                .findOne({isbn_code:isbn})
                .exec(function(err, bookModel){
                    if(!bookModel) = lib.db.model("Book")(bookData)
                    else bookModel.stores = mergeStores(bookModel.stores,bookData.stores)
                    bookModel.save(function(err,book){
                        if(err) return next(controller.RESTError('InternalServerError',err))
                        controller.writeHAL(res,book)
                    })
                })
        } else { next(controller.RESTError('InvalidArgumentError','Missing content of book')) }
    })
    controller.addAction({
        'path': '/books/{id}/authors',
        'method': 'GET',
        'params': [swagger.pathParam('id','The ID of the book','int')],
        'summary': 'Returns the list of authors of one specific book',
        'nickname': 'getAuthors'
    }, function(req,res,next){
        var id = req.params.id
        if(id){
            lib.db.model("Book")
                .findOne({_id:id})
                .populate('authors')
                .exec(function(err,book){
                    if(err) return next(controller.RESTError('InternalServerError',err))
                    if(!book) return next(controller.RESTError('ResourceNotFoundError','Book not found'))
                    controller.writeHAL(res,book.authors)
                })
        } else { next(controller.RESTError('InvalidArgumentError', 'Missing book id')) }
    })
    controller.addAction({
        'path': '/books/{id}/reviews',
        'method':'GET',
        'params': [ swagger.pathParam('id', 'The Id of the book','int') ],
        'summary': 'Returns the list of reviews of one specific book',
        'nickname': 'getBooksReviews'
    }, function(req,res,next){
        var id = req.params.id
        if(id){
            lib.db.model("Book")
                .findOne({_id: id})
                .populate('reviews')
                .exec(function(err, book) {
                    if(err) return next(controller.RESTError('InternalServerError', err))
                    if(!book) return next(controller.RESTError('ResourceNotFoundError', 'Book not found'))
                    controller.writeHAL(res, book.reviews)
                })
        } else { next(controller.RESTError('InvalidArgumentError', 'Missing book id')) }
    })
    controller.addAction({
        'path': '/books/{id}',
        'method': 'PUT',
        'params': [
                    swagger.pathParam('id', 'The Id of the book to update','string'),
                    swagger.bodyParam('book', 'The data to change on the book','string')
        ],
        'summary': 'Updates the information of one specific book',
        'nickname': 'updateBook'
    }, function(req,res,next){
        var data = req.body
        var id = req.params.id
        if(id) {
            lib.db.model("Book")
            .findOne({_id: id})
            .exec(function(err, book) {
                if(err) return next(controller.RESTError('InternalServerError', err))
                if(!book) return next(controller.RESTError('ResourceNotFoundError','Book not found'))
                book = _.extend(book, data)
                book.save(function(err, data) {
                    if(err) return next(controller.RESTError('InternalServerError', err))
                    controller.writeHAL(res, data.toJSON())
                })
            })
        } else { next(controller.RESTError('InvalidArgumentError', 'Invalid id received')) }
    })
    return controller
}
```

The code for this controller are the basic mechanics defined for this particular project on how to declare a controller and its actions. 

We also have a special case for the POST action, which checks the ISBN of a new book to see if it is in stock at another store. If an ISBN already exists, the book is merged to all relevant stores; otherwise, a new record is created.

In theory, we’re creating a new function that inherits from the BaseController, which gives us the ability to add custom behavior on a specific controller. Reality is going to prove that we don’t really need such liberties, however. And we could very well do the same by instantiating the BaseController directly on every other controller file.

The controller files are required during initialization of the API, and when that happens, the lib object is passed to them, like so:

```javascript
var controller = require("/controllers/books.js")(lib)
```

This means that the lib object is received by the export function, which is in charge of instantiating the new controller and adding actions to it to return it to the required code.
Here are some other interesting bits from the code:

* The getBooks action shows how to do simple regular expression–based filtering with Mongoose.
* The update action is not actually using the update method from Mongoose, but instead loads the model using the extend method from the underscore, and finally calls the save method on the model. This is done for one simple reason: the update method doesn’t trigger any post hooks on the models, but the save method does, so if we wanted to add behavior to react to an update on the model, this would be the way to go about it.

*/controllers/stores.js*

```javascript
var
    BaseController = require("./basecontroller"),
    _ = require("underscore"),
    swagger = require("swagger-node-restify")

function Stores(){}

Stores.prototype = new BaseController()


module.exports = function(lib){


    var controller = new Stores()


    controller.addAction({
        'path':'/stores',
        'method':'GET',
        'summary':'Returns the list of stores',
        'params': [swagger.queryParam('state','Filter the list of stores by state','string')],
        'responseClass':'Store',
        'nickname':'getStores',
    }, (req,res,next) => {
        var criteria = {}
        if(req.params.state) criteria.state = new RegExp(req.params.state, 'i')
        lib.db.model('Store')
            .find(criteria)
            .exec((err,list)=>{
                if(err) return next(controller.RESTError('InternalServerError', err))
                controller.writeHAL(res, list)
            })

    })

    controller.addAction({
        'path': '/stores/{id}',
        'method': 'GET',
        'summary':'Returns the data of a store',
        'params': [swagger.queryParam('id','The id of the store','string')],
        'responseClass':'Store',
        'nickname':'getStore',
    }, (req,res,next) => {
        var id = req.params.id
        if(id){
            lib.db.model('Store')
                .findOne({_id:id})
                .populate('employees')
                .exec((err,data) => {
                    if(err) return next(controller.RESTError('InternalServerError',err))
                    if(!data) return next(controller.RESTError('ResourceNotFoundError','Store not found'))
                    controller.writeHAL(res,data)
                })
        } else { next(controller.RESTError('InvalidArgumentError','Invalid id')) }
    })

    controller.addAction({
        'path': '/stores/{id}/books',
        'method': 'GET',
        'summary':'Returns the list of books of a store',
        'params': [
            swagger.queryParam('id','The id of the store','string'),
            swagger.queryParam('q', 'Search parameter for the books','string'),
            swagger.queryParam('genre','Filter results by genre','string')
        ],
        'responseClass':'Book',
        'nickname':'getStoreBooks',
    }, (req,res,next) => {
        var id = req.params.id
        if(id){
            var criteria = {stores:id}
            if(req.params.q){
                var expr = new RegExp('.*'+req.params.q+'.*','i')
                criteria.$or = [
                    {title:expr},
                    {isbn_code:expr},
                    {description:expr}
                ]
            }
            if(req.params.genre) criteria.genre = req.params.genre
            lib.db.model('Book')
                .find(criteria)
                .populate('authors')
                .exec((err,data) => {
                    if(err) return next(controller.RESTError('InternalServerError',err))
                    controller.writeHAL(res,data)
                })
        } else { next(controller.RESTError('InvalidArgumentError','Invalid id'))}
    })

    controller.addAction({
        'path':'/stores/{id}/employees',
        'method': 'GET',
        'summary':'Returns the list of employees working on a store',
        'params': [swagger.queryParam('id','The id of the store','string')],
        'responseClass':'Employee',
        'nickname':'getStoreEmployees',
    }, (req,res,next) => {
        var id = req.params.id
        if(id){
            lib.db.model('Store')
                .findOne({_id:id})
                .populate('employees')
                .exec((err,data) => {
                    if(err) return next(controller.RESTError('InternalServerError',err))
                    if(!data) return next(controller.RESTError('ResourceNotFoundError','Store not found'))
                    console.log(data)
                    controller.writeHAL(res,data.employees)
                })
        } else { next(controller.RESTError('InvalidArgumentError','Invalid id'))}
    })

    controller.addAction({
        'path': '/stores/{id}/booksales',
        'method':'GET',
        'summary':'Returns the list of booksales done on a store'
        'params': [swagger.queryParam('id','The id of the store','string')],
        'responseClass':'BookSale',
        'nickname':'getStoresBookSales',
    }, (req,res,next) => {
        var id = req.params.id
        if(id){
            lib.db.model('Booksale')
                .find({store:id})
                .populate('client')
                .populate('employee')
                .populate('books')
                .exec((err,data) => {
                    if(err) return next(controller.RESTError('InternalServerError',err))
                    controller.writeHAL(res,data)
                })
        } else { next(controller.RESTError('InvalidArgumentError','Invalid id'))}
    })

    controller.addAction({
        'path':'/stores',
        'method':'POST',
        'summary': 'Adds a new store to the list',
        'params': [swagger.queryParam('store','The JSON data of the store','string')],
        'responseClass':'Store',
        'nickname':'newStore',
    }, (req,res,next) => {
        var data = req.body
        if(data){
            var newStore = lib.db.model('Store')(data)
            newStore.save(function(err,store){
                if(err) return next(controller.RESTError('InternalServerError',err))
                res.json(controller.toHAL(store))
            })
        } else { next(controller.RESTError('InvalidArgumentError','No data received'))}
    })

    controller.addAction({
        'path':'/stores/{id}',
        'method':'PUT',
        'summary':"Updates a store's information",
        'params': [
            swagger.queryParam('id','The id of the store','string'),
            swagger.queryParam('store','The new information to update','string')
        ],
        'responseClass':'Store',
        'nickname':'updateStore',
    }, (req,res,next) => {
        var data = req.body
        var id = req.params.id
        if(id){
            lib.db.model('Store')
                .findOne({_id:id})
                .exec((err,store)=>{
                    if(err) return next(controller.RESTError('InternalServerError',err))
                    if(!store) return next(controller.RESTError('ResourceNotFoundError','Store not found'))
                    store = _.extend(store,data)
                    store.save((err,data)=>{
                        if(err) return next(controller.RESTError('InternalServerError',err))
                        res.json(controller.toHAL(data))
                    })
                })
        } else { next(controller.RESTError('InvalidArgumentError','Invalid id'))}
    })

    return controller
}
```

The code for the Stores controller is very similar to that of the Books controller. 

It does, however, have something of notice: the getStoresBookSales action clearly shows what happens when we don’t use a Hierarchical MVC model. I said that this is not a common case, so it would be fine for the purpose of this book, but it shows how separation of concerns is broken in the strictest of senses by acting over the model of another controller, instead of going through that other controller. 

Given the added complexity that mechanism would imply to our code, we’re better off looking the other way for the time being.

Here are the remaining controllers. They don’t particularly show anything new compared to the
previous ones, so just look at the code and the occasional code comment.

*/controllers/authors.js*

```javascript
var
    BaseController = require('./basecontroller'),
    swagger = require('swagger-node-restify')

function BookSales(){}

BookSales.prototype = new BaseController()


module.exports = function(lib){

    var controller = new BookSales()

    controller.addAction({
        'path':'/authors',
        'method':'GET',
        'summary':'Returns the list of authors across all sites',
        'responsClass':'Author',
        'nickname':'getAuthors',
        'params':[swagger.queryParam('q','Search parameter','string')]
    },(req,res,next)=>{
        var criteria = {},
            filterByGenre = false || req.params.genre
        if(req.params.q){
            var expr = new RegExp('.*'+req.params.q+'.*','i')
            criteria.$or = [
                {name:expr},
                {description:expr}
            ]
        }
        if(filterByGenre){
            lib.db.model('Book')
                .find({genre:filterByGenre})
                .exec((err,books)=>{
                    if(err) return next(controller.RESTError('InternalServerError',err))
                    findAuthors(_.pluck(books,'_id'))
                })
        } else { findAuthors() }
        function findAuthors(bookIds){
            if(bookIds) criteria.books = {$in:bookIds}
            lib.db.model('Author')
                .find(criteria)
                .exec(function(err, authors) {
                    if(err) return next(controller.RESTError('InternalServerError',err))
                    controller.writeHAL(res, authors)
                }
        }
    })

    controller.addAction({
        'path':'/authors/{id}',
        'method':'GET',
        'summary':'Returns all the data from one specific author',
        'responsClass':'Author',
        'nickname':'getAuthor'
    },(req,res,next)=>{
        var id = req.params.id
        if(id){
            lib.db.model('Author')
                .findOne({_id: id})
                .exec(function(err, author) {
                    if(err) return next(controller.RESTError('InternalServerError',err))
                    if(!author) return next(controller.RESTError('ResourceNotFoundError', 'Author not found'))
                    controller.writeHAL(res, author)
                })
        } else { next(controller.RESTError('InvalidArgumentError', 'Missing author id')) }
    })

    controller.addAction({
        'path':'/authors',
        'method':'POST',
        'summary':'Adds a new author',
        'responsClass':'Author',
        'nickname':'addAuthor',
        'params':[swagger.bodyParam('author','JSON representation of the data','string')]
    },(req,res,next)=>{
        var body = req.body
        if(body){
            var newAuthor = lib.db.model('Author')(body)
            newAuthor.save((err, author)=>{
                if(err) return next(controller.RESTError('InternalServerError', err))
                controller.writeHAL(res, author)
            })
        } else { next(controller.RESTError('InvalidArgumentError', 'Missing author id')) }
    })

    controller.addAction({
        'path':'/authors/{id}',
        'method':'PUT',
        'summary':"UPDATES an author's information",
        'responsClass':'Author',
        'nickname':'updateAuthor',
        'params':[
            swagger.pathParam('id','The id of the author','string'),
            swagger.pathParam('author','The new information to update','string')
        ]
    }, (req,res,next)=>{
        var data = req.body,
            id = req.params.id
        if(id){
            lib.db.model("Author")
                .findOne({_id: id})
                .exec((err, author)=>{
                    if(err) return next(controller.RESTError('InternalServerError', err))
                    if(!author) return next(controller.RESTError('ResourceNotFoundError','Author not found'))
                    author = _.extend(author, data)
                    author.save((err, data)=>{
                        if(err) return next(controller.RESTError('InternalServerError', err))
                        res.json(controller.toHAL(data))
                    })
                })
        } else { next(controller.RESTError('InvalidArgumentError', 'Invalid id received')) }

    })

    controller.addAction({
        'path':'/authors/{id}/books',
        'method':'GET',
        'summary':'Returns the data from all the books of one specific author',
        'responsClass':'Book',
        'nickname':'getAuthorsBook',
        'params':[swagger.pathParam('id','The id of the author','string')]
    }, (req,res,next)=>{
        var id = req.params.id
        if(id){
            lib.db.model('Author')
                .findOne({_id: id})
                .populate('books')
                .exec((err, author) => {
                    if(err) return next(controller.RESTError('InternalServerError', err))
                    if(!author) return next(controller.RESTError('ResourceNotFoundError', 'Author not found'))
                    controller.writeHAL(res, author.books)
                })
        } else { next(controller.RESTError('InvalidArgumentError', 'Missing author id')) }
    })

    return controller
}
```

*/controllers/booksales.js*

```javascript

```
