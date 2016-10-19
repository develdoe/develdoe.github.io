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
 var BaseController = require("./basecontroller"),
     swagger = require("swagger-node-restify")

function BookSales() {}

BookSales.prototype = new BaseController()

module.exports = function(lib) {
    var controller = new BookSales();

    controller.addAction({
           'path': '/booksales',
           'method': 'GET',
           'summary': 'Returns the list of book sales',
           'responseClass': 'BookSale',
           'nickname': 'getBookSales',
           'params': [
               swagger.queryParam('start_date', 'Filter sales done after (or on) this date', 'string'),
               swagger.queryParam('end_date', 'Filter sales done on or before this date', 'string'),
               swagger.queryParam('store_id', 'Filter sales done  on this store', 'string')
            ]
    }, function(req, res, next) {
        console.log(req)
        var criteria = {}
        if(req.params.start_date) criteria.date = {$gte: req.params.start_date}
        if(req.params.end_date) criteria.date = {$lte: req.params.end_date}
        if(req.params.store_id) criteria.store = req.params.store_id
        lib.db.model("Booksale")
            .find(criteria)
            .populate('books')
            .populate('client')
            .populate('employee')
            .populate('store')
            .exec(function(err, sales) {
                if(err) return next(controller.RESTError('InternalServerError', err))
                controller.writeHAL(res, sales)
            })
    })
    controller.addAction({
        'path': '/booksales',
        'method': 'POST',
        'params': [ swagger.bodyParam('booksale', 'JSON representation of the new booksale','string') ],
        'summary': 'Records a new booksale',
        'nickname': 'newBookSale'
    }, function(req, res, next) {
        var body = req.body
        if(body) {
            var newSale = lib.db.model("Booksale")(body)
            newSale.save(function(err, sale) {
                if(err) return next(controller.RESTError('InternalServerError', err))
                controller.writeHAL(res, sale)
            })
        } else { next(controller.RESTError('InvalidArgumentError', 'Missing json data'))}
    })
    return controller
}
```

*/controllers/clientreviews.js*

```javascript
 var
    BaseController = require("./basecontroller"),
    _ = require("underscore"),
    swagger = require("swagger-node-restify")

 function ClientReviews() {}

 ClientReviews.prototype = new BaseController()

 module.exports = function(lib) {
     var controller = new ClientReviews();

     controller.addAction({
         'path': '/clientreviews',
         'method': 'POST',
         'summary': 'Adds a new client review to a book',
         'params': [swagger.bodyParam('review', 'The JSON representation of the review',  'string')],
         'responseClass': 'ClientReview',
         'nickname': 'addClientReview'
     }, function(req, res, next) {
         var body = req.body
         if(body) {
             var newReview = lib.db.model('ClientReview')(body)
             newReview.save(function (err, rev) {
                 if(err) return next(controller.RESTError('InternalServerError', err))
                 controller.writeHAL(res, rev)
             })
         }
     })
     return controller
 }
```

*/controllers/clients.js*

```javascript
var BaseController = require("./basecontroller"),
    _ = require("underscore"),
    swagger = require("swagger-node-restify")

function Clients() {}
Clients.prototype = new BaseController()

module.exports = function(lib) {
    var controller = new Clients();

    controller.addAction({
        'path': '/clients',
        'method': 'GET',
        'summary': 'Returns the list of clients ordered by name',
        'responsClass': 'Client',
        'nickname': 'getClients'
    }, function(req, res, next) {
        lib.db.model('Client')
            .find()
            .sort('name')
            .exec(function(err, clients) {
                if (err) return next(controller.RESTError('InternalServerError', err))
                controller.writeHAL(res, clients)
            })
    })

    controller.addAction({
        'path': '/clients',
        'method': 'POST',
        'params': [swagger.bodyParam('client', 'The JSON representation of the client', 'string')],
        'summary': 'Adds a new client to the database',
        'responsClass': 'Client',
        'nickname': 'addClient'
    }, function(req, res, next) {
        var newClient = req.body
        var newClientModel = lib.db.model('Client')(newClient)
        newClientModel.save(function(err, client) {
            if (err) return next(controller.RESTError('InternalServerError', err))
            controller.writeHAL(res, client)
        })
    })

    controller.addAction({
        'path': '/clients/{id}',
        'method': 'GET',
        'params': [swagger.pathParam('id', 'The id of the client', 'string')],
        'summary': 'Returns the data of one client',
        'responsClass': 'Client',
        'nickname': 'getClient'
    }, function(req, res, next) {
        var id = req.params.id
        if (id != null) {
            lib.db.model('Client')
                .findOne({
                    _id: id
                })
                .exec(function(err, client) {
                    if (err) return next(controller.RESTError('InternalServerError', err))
                    if (!client) return next(controller.RESTError('ResourceNotFoundError', 'The client id cannot be found'))
                    controller.writeHAL(res, client)
                })
        } else {
            next(controller.RESTError('InvalidArgumentError', 'Invalid client id'))
        }
    })

    controller.addAction({
        'path': '/clients/{id}',
        'method': 'PUT',
        'params': [
            swagger.pathParam('id', 'The id of the client', 'string'),
            swagger.bodyParam('client', 'The content to overwrite', 'string')
        ],
        'summary': 'Updates the data of one client',
        'responsClass': 'Client',
        'nickname': 'updateClient'
    }, function(req, res, next) {
        var id = req.params.id
        if (!id) return next(controller.RESTError('InvalidArgumentError', 'Invalid id'))
        else {
            var model = lib.db.model('Client')
            model.findOne({
                _id: id
            })
            model.exec(function(err, client) {
                if (err) return next(controller.RESTError('InternalServerError', err))
                client = _.extend(client, req.body)
                client.save(function(err, newClient) {
                    if (err) return next(controller.RESTError('InternalServerError', err))
                    controller.writeHAL(res, newClient)
                })
            })
        }
    })
    return controller
}
```

*/controllers/employees.js*

```javascript
var
    BaseController = require("./basecontroller"),
    _ = require("underscore"),
    swagger = require("swagger-node-restify")

function Employees() {}
Employees.prototype = new BaseController()
module.exports = function(lib) {
    var controller = new Employees();

    controller.addAction({
        'path': '/employees',
        'method': 'GET',
        'summary': 'Returns the list of employees across all stores',
        'responseClass': 'Employee',
        'nickname': 'getEmployees'
    }, function(req, res, next) {
        lib.db.model('Employee').find().exec(function(err, list) {
            if (err) return next(controller.RESTError('InternalServerError', err))
            controller.writeHAL(res, list)
        })
    })

    controller.addAction({
        'path': '/employees/{id}',
        'method': 'GET',
        'params': [swagger.pathParam('id','The id of the employee','string')],
        'summary': 'Returns the data of an employee',
        'responseClass': 'Employee',
        'nickname': 'getEmployee'
  }, function(req, res, next) {
      var id = req.params.id
      if(!id) next(controller.RESTError('InvalidArgumentError', 'Invalid id'))
      else {
          lib.db.model('Employee').findOne({_id: id}).exec(function(err, empl) {
              if(err) return next(controller.RESTError('InternalServerError', err))
              if(!empl) return next(controller.RESTError('ResourceNotFoundError', 'Not found'))
              controller.writeHAL(res, empl)
          })
      }
  })

  controller.addAction({
      'path': '/employees',
      'method': 'POST',
      'params': [swagger.bodyParam('employee', 'The JSON data of the employee',
      'string')],
      'summary': 'Adds a new employee to the list',
      'responseClass': 'Employee',
      'nickname': 'newEmployee'
  }, function(req, res, next) {
      var data = req.body
      if(!data) next(controller.RESTError('InvalidArgumentError', 'No data received'))
      else {
          var newEmployee = lib.db.model('Employee')(data)
          newEmployee.save(function(err, emp) {
              if(err) return next(controller.RESTError('InternalServerError', err))
              controller.writeHAL(res, emp)
          }
      }
  })

  controller.addAction({
        'path': '/employees/{id}',
        'method': 'PUT',
        'summary': "UPDATES an employee's information",
        'params': [
                swagger.pathParam('id','The id of the employee','string'),
                swagger.bodyParam('employee', 'The new information to update', 'string')],
        'responseClass': 'Employee',
        'nickname': 'updateEmployee'
  }, function(req, res, next) {
      var data = req.body
      var id = req.params.id
      if(!id) next(controller.RESTError('InvalidArgumentError', 'Invalid id'))
      else {
          lib.db.model("Employee").findOne({_id: id}).exec(function(err, emp) {
              if(err) return next(controller.RESTError('InternalServerError', err))
              emp = _.extend(emp, data)
              emp.save(function(err, employee) {
                  if(err) return next(controller.RESTError('InternalServerError', err))
                  controller.writeHAL(res, employee)
              })
          })
      }
  })

  return controller
}
```

### lib

The lib folder contains all sorts of helper functions and utilities that were just too small to be put into a separate folder, but important and generic enough to be used in several places of the code.

*/lib/index.js*

```javascript
var lib = {
    helpers:            require("./helpers"),
    config:             require("./config"),
    controllers:        require("../controllers"),
    schemas:            require("../schemas"),
    schemaValidator:    require("./schemaValidator"),
    db:                 require("./db")
}

module.exports = lib
```

This file is supposed to act as the single point of contact between the outside world (the rest of the project) and the inside world (all of the mini-modules grouped within this folder). There is nothing special about it. It just requires everything and exports using predefined keys.

*/lib/helpers.js*

```javascript
var halson = require("halson"),
    _ = require("underscore")

module.exports = {
    makeHAL:        makeHAL,
    setupRoutes:    setupRoutes,
    validateKey:    validateKey
}

function setupRoutes(server,swagger,lib){
    for(controller in lib.controllers){
        cont = lib.controllers[controller](lib)
        cont.setUpActions(server,swagger)
    }
}

// Sign in every request and compare it
// against the key sent by the client, this way
// we make shure its authentic
function validateKey(hmacdata,key,lib){
    // This is for testing the swagger-ui, should be removed after development
    // to avoid possible security problem
    if(key == 777) return true
    var hmac = require("crypto").createHmac("md5",lib.config.secretKey).update(hmacdata).digest("hex")
    console.log(hmac) // remove this line
    return hmac == key
}

function makeHAL(data,links,embed){
    var obj = halson(data)
    if(links && links.length > 0) { _.each(links,(lnk)=>{ obj.addLink(lnk.name,{href:lnk.href,title:lnk.title || ''}) }) }
    if(embed && embed.length > 0) { _.each(embed,(item)=>{ obj.addEmbed(item.name,item.data)}) }
    return obj
}
```

Just as the modules exported by the index.js file are too small to merit their own folder, these functions are too small and particular to merit their own module, so instead they are grouped here, inside the helpers module. The functions are meant to be of use (hence, the name “helpers”) throughout the entire project:

* **setupRoutes**: This function is called from within the project’s main file during boot-up time. It’s meant to initialize all controllers, which in turn adds the actual route’s code to the HTTP server.

* **validateKey**: This function contains the code to validate the request by recalculating the HMAC key. And as mentioned earlier, it contains the exception to the rule, allowing any request to validate if the key sent is 777.

* **makeHAL**: This function turns any type of object into a HAL JSON object ready to be rendered. This particular function is heavily used from within the models’ code.

*/lib/schemaValidator.js*

```javascript
var tv4 = require("tv4"),
    formats = require("tv4-formats"),
    schemas = require("../request_schemas/")

module.exports = {
    validateRequest: validate
}

function validate (req) {
    var res = {valid: true}
    tv4.addFormat(formats)
    var schemaKey = req.route ? req.route.path.toString().replace("/", "") : ''
    var actionKey = req.route.name
    if(schemas[schemaKey]){
        var mySchema = schemas[schemaKey][actionKey]
        var data = null
        if(mySchema) {
            switch(mySchema.validate) {
                case 'params':
                    data = req.params
                    break
            }
            res = tv4.validateMultiple(data, mySchema.s
        }
    }
    return res
}
```

This file has the code that validates any request against a JSON Schema that we define. 

The only function of interest is the validate function, which validates the request object. It also counts on a predefined structure inside the request, which is added by Swagger (the route attribute).

As you might’ve guessed from the preceding code, the validation of a request is optional; not every request is being validated. And right now, only query parameters are validated, but this can be extended by simply adding a new case to the switch statement.

This function works with the premise of “convention over configuration,” which means that if you set up everything “right,” then you don’t have to do much. In our particular case, we’re looking inside the request_ schemas folder to load a set of predefined schemas, which have a very specific format. In that format we find the name of the action (the nickname that we set up) to validate and the portion of the request we want to validate. 

In our particular function, we’re only validating query parameters for things such as invalid formats and so forth. The only request we have set up to validate right now is the BookSales listing action; but if we wanted to add a new validation, it would just be a matter of adding a new schema—no programming required.

*/lib/db.js*

```javascript
var
    config = require("./config"),
    _ = require("underscore"),
    mongoose = require("mongoose"),
    Schema = mongoose.Schema

var obj = {
    cachedModels: {},
    getModelFromSchema: getModelFromSchema,
    model: function(mname) {
        return this.models[mname]
    }
    connect: function(cb) {
        mongoose.connect(config.database.host + "/" + config.database.dbname)
        this.connection = mongoose.connection
        this.connection.on('error', cb)
        this.connection.on('open', cb)
    }
}

obj.models = require("../models/")(obj)
module.exports = obj

function translateComplexType(v, strType) {
    var tmp = null
    var type = strType || v['type']

    switch (type) {
        case 'array':
            tmp = []
            if (v['items']['$ref'] != null) {
                tmp.push({
                    type: Schema.ObjectId,
                    ref: v['items']['$ref']
                })
            } else {
                var originalType = v['items']['type']
                v['items']['type'] = translateTypeToJs(v['items']
                    ['type'])
                tmp.push(translateComplexType(v['items'], originalType))
            }
            break

        case 'object':
            tmp = {}
            var props = v['properties']
            _.each(props, function(data, k) {
                if (data['$ref'] != null) {
                    tmp[k] = {
                        type: Schema.ObjectId,
                        ref: data['$ref']
                    }
                } else {
                    tmp[k] = translateTypeToJs(data['type'])
                }
            })
            break

        default:
            tmp = v
            tmp['type'] = translateTypeToJs(type)
            break
    }
    return tmp
}

// Turns the JSON Schema into a Mongoose schema
function getModelFromSchema(schema) {
    var data = {
        name: schema.id,
        schema: {}
    }
    var newSchema = {}
    var tmp = null
    _.each(schema.properties, function(v, propName) {
        if(v['$ref'] != null) {
            tmp = {
                type: Schema.ObjectId,
                ref: v['$ref']
            }
        } else { tmp = translateComplexType(v) }
        newSchema[propName] = tmp
    })
    data.schema = new Schema(newSchema)
    return data
}

function translateTypeToJs(t) {
    if(t.indexOf('int') === 0)  t = "number"
    return eval(t.charAt(0).toUpperCase() + t.substr(1))
}
```

This file contains some interesting functions that are used a lot from the models’ code. 

The schemas used with Swagger can potentially be reused to do other things, such
as defining the models’ schemas. But to do this, we need a function to translate the standard JSON Schema into the nonstandard JSON format required by Mongoose to define a model. This is where the getModelFromSchema function comes into play; its code is meant to go over the structure of the JSON Schema and create a new, simpler JSON structure to be used as a Mongoose Schema.
The other functions are more straightforward:

* **connect**: Connects to the database server and sets up the callbacks for both error and success cases.
* **model**: Accesses the model from outside. We could just directly access the models using the object models, but it’s always a good idea to provide a wrapper in case you ever need to add extra behaviors (such as checking for errors).

*/lib/config.js*

```javascript
module.exports = {
    secretKey: 'this is a secret key, right here',
    server: {
        name: 'ComeNRead API',
        version: '1.0.0',
        port: 9000
    },
    database: {
        host: 'mongodb://localhost',
        dbname: 'comenread'
    }
}
```

### models

This folder contains the actual code of each model. The definition of these resources won’t be found in these files because they’re only meant to define behavior. The actual properties are defined in the schemas folder (which, is being used both by the models and Swagger).

*/models/index.js*

```javascript
module.exports = function(db) {
    return {
        "Book": require("./book")(db),
        "Booksale": require("./booksale")(db),
        "ClientReview": require("./clientreview")(db),
        "Client": require("./client")(db),
        "Employee": require("./employee")(db),
        "Store": require("./store")(db),
        "Author": require("./author")(db)
    }
}
```

As in the other folders, the index.js file allows us to require every model at once, and treat this folder like a module itself. 

The other thing of note here is the passing of the db object to every model, so that they can access the getModelFromSchema function.

*/models/author.js*

```javascript
 var
     mongoose = require("mongoose"),
     jsonSelect = require('mongoose-json-select'),
     helpers = require("../lib/helpers"),
     _ = require("underscore")

 module.exports = function(db) {
     var schema = require("../schemas/author.js")
     var modelDef = db.getModelFromSchema(schema)
     modelDef.schema.plugin(jsonSelect, '-books')
     modelDef.schema.methods.toHAL = function() {
         var halObj = helpers.makeHAL(this.toJSON(), [{
             name: 'books',
             'href': '/authors/' + this.id + '/books',
             'title': 'Books'
         }])
         if (this.books.length > 0) {
             if (this.books[0].toString().length != 24) {
                 halObj.addEmbed('books', _.map(this.books,
                     function(e) {
                         return e.toHAL()
                     }))
             }
         }
         return halObj
     }
     return mongoose.model(modelDef.name, modelDef.schema)
 }
```

The code of the Author model shows the basic mechanics of loading the JSON Schema, transforming it into a Mongoose Schema, defining the custom behavior, and finally returning a new model.
The following defines the main custom behaviors:

* The jsonSelect model allows us to define the attributes to add to or remove
from the object when turning it into a JSON. We want to remove the embedded objects from the JSON representation, because they will be added to the HAL JSON representation as embedded objects, rather than being part of the main object.
* The toHAL method takes care of returning the representation of the resource in JSON HAL format.
* The links associated to the main object are defined manually. We could improve this by further customizing the code for the loading and transformation of the JSON Schemas of the models.

**The code below determine if the model has populated a reference, or if it is simply the id of the referenced object**

```javascript 
if(this.books[0].toString().length != 24) {
  //...
}
```

The following is the rest of the code inside the models folder, as you can appreciate, the same mechanics are duplicated on every case.

*/models/book.js*

```javascript
var mongoose = require("mongoose"),
    jsonSelect = require('mongoose-json-select'),
    helpers = require("../lib/helpers"),
    _ = require("underscore")

module.exports = function(db) {
    var schema = require("../schemas/book.js")
    var modelDef = db.getModelFromSchema(schema)
    modelDef.schema.plugin(jsonSelect, '-stores -authors')
    modelDef.schema.methods.toHAL = function() {
        var halObj = helpers.makeHAL(this.toJSON(), [{
            name: 'reviews',
            href: '/books/' + this.id + '/reviews',
            title: 'Reviews'
        }])
        if(this.stores.length > 0) { if(this.stores[0].store.toString().length != 24) { halObj.addEmbed('stores', _.map(this.stores, function(s) { return { store: s.store.toHAL(), copies: s.copies } }))}}
        if(this.authors.length > 0) { if(this.authors[0].toString().length != 24) { halObj.addEmbed('authors', this.authors) }}
        return halObj
    }
    return mongoose.model(modelDef.name, modelDef.schema)
}
```

*/models/booksale.js*

```javascript
var mongoose = require("mongoose"),
    jsonSelect = require('mongoose-json-select'),
    helpers = require("../lib/helpers"),
    _ = require("underscore")

module.exports = function(db) {
    var schema = require("../schemas/booksale.js")
    var modelDef = db.getModelFromSchema(schema)
    modelDef.schema.plugin(jsonSelect, '-store -employee -client -books')
    modelDef.schema.methods.toHAL = function() {
        var halObj = helpers.makeHAL(this.toJSON())
        if(this.books.length > 0) { if(this.books[0].toString().length != 24) { halObj.addEmbed('books', _.map(this.books,function(b) { return b.toHAL() }))}}
        if(this.store.toString().length != 24) halObj.addEmbed('store', this.store.toHAL())
        if(this.employee.toString().length != 24) halObj.addEmbed('employee', this.employee.toHAL())
        if(this.client.toString().length != 24) halObj.addEmbed('client', this.client.toHAL())
        return halObj
    }
    return mongoose.model(modelDef.name, modelDef.schema)
}
```

*/models/client.js*

```javascript
var mongoose = require("mongoose"),
    jsonSelect = require('mongoose-json-select'),
    helpers = require("../lib/helpers"),
    _ = require("underscore")

module.exports = function(db) {
    var schema = require("../schemas/client.js")
    var modelDef = db.getModelFromSchema(schema)
    modelDef.schema.methods.toHAL = function() {
        var halObj = helpers.makeHAL(this.toJSON())
        return halObj
    }
    return mongoose.model(modelDef.name, modelDef.schema)
}
```

*/models/clientreview.js*

```javascript
var mongoose = require("mongoose"),
    jsonSelect = require('mongoose-json-select'),
    helpers = require("../lib/helpers"),
    _ = require("underscore")


module.exports = function(db) {
    var schema = require("../schemas/clientreview.js")
    var modelDef = db.getModelFromSchema(schema)
    modelDef.schema.methods.toHAL = function() {
        var halObj = helpers.makeHAL(this.toJSON())
        return halObj
    }
    modelDef.schema.post('save', function(doc, next) {
        db.model('Book').update({_id: doc.book}, {$addToSet: {reviews: this.id}}, function(err) { next(err) })
    })
    return mongoose.model(modelDef.name, modelDef.schema)
}
```

*/models/employee.js*

```javascript
var mongoose = require("mongoose"),
    jsonSelect = require('mongoose-json-select'),
    helpers = require("../lib/helpers"),
    _ = require("underscore")

module.exports = function(db) {
    var schema = require("../schemas/employee.js")
    var modelDef = db.getModelFromSchema(schema)
    modelDef.schema.methods.toHAL = function() {
        var halObj = helpers.makeHAL(this.toJSON())
        return halObj
    }
    return mongoose.model(modelDef.name, modelDef.schema)
}
```

*/models/store.js*

```javascript
var mongoose = require("mongoose"),
    jsonSelect = require("mongoose-json-select"),
    _ = require("underscore"),
    helpers = require("../lib/helpers")


module.exports = function(db) {
    var schema = require("../schemas/store.js")
    var modelDef = db.getModelFromSchema(schema)
    modelDef.schema.plugin(jsonSelect, '-employees')
    modelDef.schema.methods.toHAL = function() {
        var halObj = helpers.makeHAL(this.toJSON(), [{
            name: 'books',
            href: '/stores/' + this.id + '/books',
            title: 'Books'
        }, {
            name: 'booksales',
            href: '/stores/' + this.id + '/booksales',
            title: 'Book Sales'
        }])
        if(this.employees.length > 0) { if(this.employees[0].toString().length != 24) { halObj.addEmbed('employees', _.map(this.employees,function(e) { return e.toHAL() }))}}
        return halObj
    }
    var model = mongoose.model(modelDef.name, modelDef.schema)
    return model
}
```

### request_schemas

This folder contains the JSON Schemas that will be used to validate the requests. 

They need to describe an object and its properties. We should be able to validate against the request object attribute that contains the parameters (normally request.params, but potentially something else, such as request.body).

Due to the type of attributes we defined for our endpoints, there is really only one endpoint that we would want to validate: the getBookSales (GET /booksales) endpoint. It receives two date parameters, and we probably want to validate their format to be 100% certain that the dates are valid.

Again, to provide the simplicity of usage that “convention over configuration” provides, our schema files must follow a very specific format, which is then used by the validator that we saw earlier:

```javascript
/request_schemas/ [CONTROLLER NAME].js module.exports = {
    [ENDPOINT NICKNAME]: {
        validate: [TYPE],
        schema: [JSON SCHEMA]
    }
}
```

There are several pieces that need to be explained in the preceding code:

* **CONTROLLER NAME**: This means that the file for the schema needs to have the same name as the controller, all lowercase. And since we already did that for our controllers’ files, this mean the schemas for each controller will have to have the same name as each controller’s file.
* **ENDPOINT NICKNAME**: This should be the nickname given to the action when adding it to the controller (using the addAction method).
* **TYPE**: The type of object to validate. The only value supported right now is params, which references the query and path parameters received. This could be extended to support other objects.
* **JSON SCHEMA**: This is where we add the actual JSON Schema defining the request parameters.

Here is the actual code defining the validation for the getBookSales action:

*/request_schemas/booksales.js*

```javascript
module.exports = {
    getbooksales: {
        validate: 'params',
        schema: {
            type: "object",
            properties: {
                start_date: {
                    type: 'string',
                    format: 'date'
                },
                end_date: {
                    type: 'string',
                    format: 'date'
                },
                store_id: {
                    type: 'string'
                }
            }
        }
    }
}
```

### Schemas

This folder contains the JSON Schema definitions of our resources, which also translate into the Mongoose Schemas when initializing our models.

The level of detail provided in these files is very important, because it also translates into the actual Mongoose model. This means that we could define things such as ranges of values and format patterns, which would be validated by Mongoose when creating the new resources.

For instance, let’s take a look at ClientReview, a schema that makes use of such capability:

*/schemas/clientreview.js*

```javascript
module.exports = {
    "id": "ClientReview",
    "properties": {
        "client": {
            "$ref": "Client",
            "description": "The client who submits the review"
        },
        "book": {
            "$ref": "Book",
            "description": "The book being reviewed"
        },
        "review_text": {
            "type": "string",
            "description": "The actual review text"
        },
        "stars": {
            "type": "integer",
            "description": "The number of stars, from 0 to 5",
            "min": 0,
            "max": 5
        }
    }
}
```

The stars attribute is clearly setting the maximum and minimum values that we can send when saving a new review. If we tried to send an invalid number, then we would get an error.

When defining schemas that reference others, remember to correctly name the reference (the name of each schema is given by the id property). So if you correctly set up the reference, the getModelFromSchema method of the db module will also properly set up the reference in Mongoose (this works both for direct reference and for collections).

Here is the main file for this folder; the index.js works like the index files in the other folders:

*/schemas/index.js*

```javascript
module.exports = {
    models: {
        BookSale: require("./booksale"),
        Book: require("./book"),
        Author: require("./author"),
        Store: require("./store"),
        Employee: require("./employee"),
        Client: require("./client"),
        ClientReview: require("./clientreview")
    }
}
```

The rest of the schemas defined for the project:

*/schemas/author.js*

```javascript
module.exports = {
    "id": "Author",
    "properties": {
        "name": {
            "type": "string",
            "description": "The full name of the author"
        },
        "description": {
            "type": "string",
            "description": "A small bio of the author"
        },
        "books": {
            "type": "array",
            "description": "The list of books published on at least one of the stores by this author",
            "items": {
                "$ref": "Book"
            }
        },
        "website": {
            "type": "string",
            "description": "The Website url of the author"
        },
        "avatar": {
            "type": "string",
            "description": "The url for the avatar of this author"
        }
    }
}
```

*/schema/book.js*

```javascript
module.exports = {
    "id": "Book",
    "properties": {
        "title": {
            "type": "string",
            "description": "The title of the book"
        },
        "authors": {
            "type":"array",
            "description":"List of authors of the book",
            "items": {
                "$ref": "Author"
            }
        },
        "isbn_code": {
            "description": "Unique identifier code of the book",
            "type":"string"
        },
        "stores": {
            "type": "array",
            "description": "The stores where clients can buy this book",
            "items": {
                "type": "object",
                "properties": {
                    "store": {
                        "$ref": "Store",
                    },
                    "copies": {
                        "type": "integer"
                    }
                }
            }
        },
        "genre": {
            "type": "string",
            "description": "Genre of the book"
        },
        "description": {
            "type": "string",
            "description": "Description of the book"
        },
        "reviews": {
            "type": "array",
            "items": {
                "$ref": "ClientReview"
            }
        },
        "price": {
            "type": "number",
            "minimun": 0,
            "description": "The price of this book"
        }
    }
}
```

*/schemas/booksales.js*

```javascript
module.exports = {
    "id": "BookSale",
    "properties": {
        "date": {
            "type": "date",
            "description": "Date of the transaction"
        },
        "books": {
            "type": "array",
            "description": "Books sold",
            "items": {
                "$ref": "Book"
            }
        },
        "store": {
            "type": "object",
            "description": "The store where this sale took place",
            "type": "object",
            "$ref": "Store"
        },
        "employee": {
            "type": "object",
            "description": "The employee who makes the sale",
            "$ref": "Employee"
        },
        "client": {
            "type": "object",
            "description": "The person who gets the books",
            "$ref": "Client",
        },
        "totalAmount": {
            "type": "integer"
        }
    }
}
```

*/schemas/client.js*

```javascript
module.exports = {
    "id": "Client",
    "properties": {
        "name": {
            "type": "string",
            "description": "Full name of the client"
        },
        "address": {
            "type": "string",
            "description": "Address of residence of this client"
        },
        "phone_number": {
            "type": "string",
            "description": "Contact phone number for the client"
        },
        "email": {
            "type": "string",
            "description": "Email of the client"
        }
    }
}
```

/schemas/employee.js

```javascript
module.exports = {
    "id": "Employee",
    "properties": {
        "first_name": {
            "type": "string",
            "description": "First name of the employee"
        },
        "last_name": {
            "type": "string",
            "description": "Last name of the employee"
        },
        "birthdate": {
            "type": "string",
            "description": "Date of birth of this employee"
        },
        "address": {
            "type": "string",
            "description": "Address for the employee"
        },
        "phone_numbers": {
            "type": "array",
            "description": "List of phone numbers of this employee",
            "items": {
                "type": "string"
            }
        },
        "email": {
            "type": "string",
            "description": "Employee's email"
        },
        "hire_date": {
            "type": "string",
            "description": "Date when this employee was hired"
        },
        "employee_number": {
            "type": "number",
            "description": "Unique identifier of the employee"
        }
    }
}
```

*/schemas/store.js*

```javascript
module.exports = {
    "id": "Store",
    "properties": {
        "name": {
            "type": "string",
            "description": "The actual name of the store"
        },
        "address": {
            "type": "string",
            "description": "The address of the store"
        },
        "state": {
            "type": "string",
            "description": "The state where the store resides"
        },
        "phone_numbers": {
            "type": "array",
            "description": "List of phone numbers for the store",
            "items": {
                "type": "string"
            }
        },
        "employees": {
            "type": "array",
            "description": "List of employees of the store",
            "items": {
                "$ref": "Employee"
            }
        }
    }
}
```

### swagger-ui

This folder contains the downloaded Swagger UI project, so we will not go over this particular code; however, I will mention the minor modifications we’ll need to do to the index.html file to get the UI to properly load.

1. Edit the routes for all the resources loaded (CSS and JS files) to start with
swagger-ui/.
2. Change the URL for the documentation server to http://localhost:9000/api-
docs (around line 31).
3. Uncomment the block of code in line 73. Set the right value to the apiKey
variable (set it to 777).

### Root Folder

This is the root of the project. There are only two files here: the main index.js and the package.json file that contains the dependencies and other project attributes.

*/package.json*

```javascript
{
    "name": "come_n_read",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
    },
    "author": "",
    "license": "ISC",
    "dependencies": {
        "colors": "^1.0.3",
        "halson": "^2.3.1",
        "mongoose": "^3.8.23",
        "mongoose-json-select": "^0.2.1",
        "restify": "^2.8.5",
        "swagger-node-restify": "^0.1.2",
        "tv4": "^1.1.9",
        "tv4-formats": "^1.0.0",
        "underscore": "^1.7.0"
    }
}
```

The most interesting part of this file is the list of dependencies. The rest was autogenerated using the init option of the npm command-line tool.

*/index.js*

```javascript
var restify = require("restify"),
    colors  = require("colors"),
    swagger = require("swagger-node-restify"),
    config  = lib.config

var server = restify.createServer(lib.config.server)

// Middleware setup
server.use(restify.queryParser())
server.use(restify.bodyParser())

restify.defaultResponseHeaders = function(data){
    this.head('Access-Control-Allow-Origin','*')
}

// Middleware to check for valid api key sent
server.use((req,res,next)=>{
    // Move forward if we're dealing with the swagger-ui or a valid key
    if(req.url.indexOf("swagger-ui") !=-1 || lib.helpers.validateKey(req.headers.hmacdata || '', req.params.api_key, lib)){ next() }
    else { req.send(401, {error:true,msg:'Invalid api key sent'}) }
})

// Vlidate each request, as long as there is a schema for it
server.use((req,res,next)=>{
    var results = lib.schemaValidator.validateRequest(req)
    if(!result.valid) res.send(400,result)
    else next()
})

// The swagger-ui is inside the "sagger-ui" folder
server.get(/^\/swagger-ui(\/.*)?/, restify.serveStatic({
    directory: __dirname + '/',
    default: 'index.html'
}))

// serup section
swagger.addModels(lib.schemas)
swagger.setAppHandler(server)
lib.helpers.setupRoutes(server,swagger,lib)
swagger.configureSwaggerPaths("","/api-docs","")  //we remove the {format} part of the paths, to
swagger.configure('http://localhost:9000', '0.1')

// Start the server
server.listen(config.server.port, () => {
     console.log("Server started succesfully...".green)
     lib.db.connect((err) => {
         if(err) console.log("Error trying to connect to database: ".red,err.red)
         else console.log("Database service successfully started".green)
     })
})
```

nd finally, the main file, the one that starts it all up, the index.js. There are four distinct sections to this file:

* The initial section, which requires all needed modules and instantiates the server.
* The middleware setup section, which handles setting up all pieces of middleware (we’ll go over this in a bit).
* The setup section, which handles loading models, controllers, setting up routes, and whatnot.
* The server start section, which starts the web server and the database client.

The initial and final sections of the file don’t really require much explanation since they’re pretty self- explanatory, so let’s go over the other two.

