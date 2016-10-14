---
date: '2016-10-13 17:49 +0200'
published: true
title: Planning a REST API
---
The final result of this article will be all the information you need to start the development process of an REST API. This article is going to use a bookstore API, as an example API.

## The case

Let’s call our fake bookstore Come&Read and assume that we’ve been asked to create a distributed API that will bring the bookstore into the twenty-first century.

Right now, it’s a pretty decent business. The bookstore currently has 10 different points of sale located across the United States. The company leadership is considering expanding into even more states. The current main problem, though, is that all of those stores have barely entered the digital era. The way of working and recordkeeping is very manual and heterogeneous; for instance:

* Some of the smaller stores keep records on paper and send a manually typed weekly report to the head store.
* While the bigger locations tend to use some sort of CRM software, there is no standard as long as numbers are exported into a common format and sent in a weekly report.
* Based on the weekly reports, the head store handles inventory of the chain-wide matters (store-specific stock, global stock, sales both per-store and global, employee records, etc.).
* Overall, the bookstore lacks web interaction with its customers, which a twenty-first century business must have. Its web site only lists addresses and phone numbers, and nothing more.

All of the secondary stores are connected by a very thin line to the main store.

The goal is to grow as a business, by not only opening new stores across the country but by also strengthening the bond between all the stores. And to achieve this, the backbone of everything will be our API. Our system will have to be a decentralized one, meaning that you’ll treat the main store just like any other store, and provide a common set of tools and data sources for every client application that might come in the future, instantly allowing for such things as the following:

* Cross-store searches
* Automatic control of global stock
* Automatic control over sales on a global level
* Dynamic data sources for things like web sites and mobile apps

The new system lives in the cloud, with all stores connected directly to it. The bond is stronger now, since everything is done automatically and every piece of information is available to all stores. Also, this new API-based system allows for the easy development of new ways to interact with potential customers, including mobile apps and dynamic web sites.

### The Specifications

Now that we know the current situation of the chain and the goal of our system, we need to start writing some hard specs. These will determine the way the system evolves and help with planning the development by giving us a better idea of the size of the project. Specifications also help us spot any design errors before we start with the implementation.

**We will not spend much time on the process of writing the system’s specs, since that subject is beyond the scope of this book. We’ll just lay down the specs and note anything that might be extremely relevant, the rest will be left to your understanding of this process.**

To provide everything mentioned, the system needs to have the following features:

* Cross-store book search/listing capabilities.
* Storage: This code is in charge of providing the information to all other entities, as well as talking directly to the data storage system that you choose.
* Sales: This feature is dedicated to allow for both in-store and online sales.
* User reviews of books: This will provide a much-needed layer of interaction between the stores and the potential clients.
* Authentication: For store employees and for customers.

The table belo describes the resources that we’ll be dealing with in this implementation:

|Resource|Properties|Description|
|:-------|----------|----------:|
|Books|Title, Authors, ISBN Code, Stores, Genre, Description, Reviews, Price|This is the main entity; it has all the properties required to identify a book and to locate it in a specific store.|
|Authors|Name, Description, Books, Website, Image/Avatar|This resource is highly related to a book’s resource because it lists the author of every book in a store.|
|Stores|Name, Address, State, Phone numbers, Employees|Basic information about each store, including the address, employees, and so forth.|
|Employees|First name, Last name, Birthdate, Address, Phone numbers, Email, HireDate, EmployeeNumber, Store|Employee information, contact data, and other internal properties that may come in handy for an admin type of user.|
|Clients|Name, Address, Phone number, Email|Basic contact information about a client.|
|BookSales|Date, Books, Store, Employee, Client, TotalAmount|The record of a book sale. It can be related to a store sale or an online sale.|
|ClientReviews|Client, Book, ReviewText, Stars|The resource in which client reviews about a book is saved. The client can enter a short free-text review and a number between 0 and 5 to represent stars.|

*Even though it’s not listed in the table above, all resources will have some database-related attributes, such as id, created_at, and updated_at, which you’ll use throughout the code.*

Based on the resources in the table above, let’s create a new table that lists the endpoints needed for each resource. The table below helps define the functionalities that each resource will have associated to it.

|Endpoint|Attributes|Method|Description|
|:-------|----------|------|----------:|
|/books|q: Optional search term. genre: Optional filtering by book genre. Defaults to “all”.|GET|Lists and searches all books. If the q parameter is present, it’s used as a free-text search; otherwise, the endpoint can be used to return lists of books by genre.|
|/books||POST|Creates a new book and saves it in the database.|
|/books/:id||GET|Returns information about a specific book.|
|/books/:id||PUT|Updates the information on a book.|
|/books/:id/authors||GET|Returns the author(s) of a specific book.|
|/books/:id/reviews||GET|Returns user reviews for a specific book.|
|/authors|genre: Optional; defaults to “all”. q: Optional search term.|GET|Returns a list of authors. If genre is present, it’s used to filter by the type of book published. If q is present, it’s used to do a free- text search on the author’s information.|
|/authors||POST|Adds a new author.|
|/authors/:id||PUT|Updates the data on a specific author.|
|/authors/:id||GET|Returns the data on a specific author.|
|/authors/:id/books||GET|Returns a list of books written by a specific author.|
|/stores|state: Optional; filters the list of stores by state name.|GET|Returns the list of stores.|
|/stores||POST|Adds a new store to the system.|
|/stores/:id||GET|Returns the data on a specific store.|
|/stores/:id/books|q: Optional; does a ull-text search of books within a specific store.|GET|Returns a list of books that are in stock at a specific store. If the attribute q is used, it performs a full-text search on those books.|
|/stores/:id/employees||GET|Returns a list of the employees working at a specific store.|
|/stores/:id/booksales||GET|Returns a list of the sales at a specific store.|
|/stores/:id||PUT|Updates the information about a specific store.|
|/employees||GET|Returns a full list of the employees working across all stores.|
|/employees||POST|Adds a new employee to the system.|
|/employees/:id||GET|Returns the data on a specific employee.|
|/employees/:id||PUT|Updates the data on a specific employee.|
|/clients||GET|Lists clients ordered alphabetically by name.|
|/clients||POST|Adds a new client to the system.|
|/clients/:id||GET|Returns the data on a specific client.|
|/clients/:id||PUT|Updates the data on a specific client.|
|/booksales|start_date: Filters records that were created after this date. end_date: Optional; filters records that were created before this date. store_id: Optional; filters records by store.|GET|Returns a list of sales. The results can be filtered by time range or by store.|
|/booksales||POST|Records a new book sale.|
|/clientreviews||POST|Saves a new client review of a book.|

*Even though they’re not specified, all endpoints that deal with listing resources will accept the following attributes: page (starting at 0, the page number to return); perpage (the number of items per page to return); and a special attribute called sort, which contains the field name by which to sort the results and the order in the following format: [FIELD_NAME]_[ASC|DESC] (e.g., title_asc).*

The table above gives us a pretty good idea of the size of the project. With it we’re able to estimate the amount of work that we have ahead of us.

The authentication scheme will be simple. As discussed in previuos articles, we’ll use the stateless alternative by signing every request with a MAC (message authentication code). The server will re-create that code to verify that the request is actually valid. This means there will not be a signing process embedded into our system, that can be done by the client. No need to worry about that for now.

**Since it’s not part of the scope of this article, the API will not handle charging for the book sales. This means that we’ll assume that the book sale was done outside of our system, and that another system will post the results into our API to keep a record of it. In a production system, this is a good way to handle this functionality inside the API itself, thus providing a complete solution.**

#### Keeping Track of Stock per Store

Every book tracks which stores it is being sold at. What is not completely clear, however, is what happens if there is more than one copy of the same book per store.

To keep track of this number, let’s enhance the relation between the books and the stores models by assigning another element: the number of copies. This is how the system will keep global stock of every book.


#### Choosing a Database Storage System

Lets go over some of the most common choices for a database storage system. I’ll talk a bit about each one and we’ll decide on one of them.

The bottom line is that all the solutions are valid, you could very well go with any of them, but we’ll need to choose one in the end, so let’s define what it is needed in the database system:

* Speed of development: Because you want the process to go quickly and not have interaction with the database be a bottleneck, you need something that integrates easily.
* Easy-to-change schema: With everything predefined, you have a pretty solid idea of what the schema is going to look like, but you might want to adjust things during development. It’s always better if the storage you’re using allows for this without a lot of hustle.
* Ability to handle entity relations: This means that key/value stores are out of the question.
* Seamless integration between the entities’ code and the database representation of the data.

We want something that can be integrated fast, changed easily, and is not key/value. Therefore, the options are as follows:

* MySQL: A classic choice when it comes to relational databases.
* PostgreSQL: Another great choice when it comes to relational database engines.
* MongoDB: A document-based NoSQL database engine.

Let’s analyze how well each one of them complies with our requirements.

##### Fast Integration

Integration with the system means how easily the modules interact with the specific database engine. 

With MySQL and PostgreSQL, there is Sequelize, which provides very complete and powerful object-relational mapping (ORM). It lets you focus more on the data model than on the actual engine particularities. Besides, if you use it right, you can potentially switch between both engines with minimum impact on the code.

On the other hand, with MongoDB you have Mongoose.js, which allows you to abstract your code from the engine, simplifying your task when it comes to defining the schemas, validations, and so forth.

##### Easy-to-Change Schemas

The fixed structure provided by both MySQL and PostgreSQL makes it harder to maintain dynamic schemas.  

Every time you make a change, you’ll need to update the schema by running migrations. The lack of structure provided by the NoSQL engines makes MongoDB the perfect choice for our project, because making a change on the schema is as simple as making the changes on the definition code, no migration or anything else required.


##### Ability to Handle Entity Relations

Since we’re leaving out key/value stores like Redis, all of our three options are able to handle entity relations. 

Both MySQL and PostgreSQL are especially good at this, since they’re both relational database engines. But let’s not rule out MongoDB, it is document-based NoSQL storage, which in turn allows you to have documents (that translate directly into a MySQL record) and subdocuments, which are a special kind of relation that we don’t have with our relational options.

Subdocument relations help to simplify both schemas and queries when working with the data. Most of our relations are based on aggregation, so this might be a good way to solve that.


##### Seamless Integration Between Our Models and the Database Entities

This is more of a comparison between Sequelize and Mongoose. Since they both abstract the storage layer, you need compare how that abstraction affects this point.

Ideally, we want our entities (our resources’ representations in the code) to be passed to our storage layer or to interact with the storage layer. 

We don’t want to require an extra type of object, usually called a DTO (data transfer object), to transfer the state of our entities between layers. Luckily, the entities provided by Sequelize and by Mongoose fall into this category, so we might as well call it a draw.

##### Summarize

We need to pick one, so let’s summarize:

* Fast integration: Let’s give this one to Sequelize, since it comes with the added bonus of being able to switch engines with minimum impact.
* Easy-to-change schemas: MongoDB wins this one, hands down.
* Handling of entity relations: I’d like to give this one to MongoDB as well, mainly due to the subdocuments feature.
* Seamless integration with our data models: This one is a draw, so we’re not counting it.

The final result seems to point toward MongoDB, but it’s a pretty close win, so in the end, personal experience needs to be taken into account as well. MongoDB is very interesting alternative when prototyping and creating something new, something that might change during the development process many times, and this is why we’ll go with it for our development. This way there is the extra insurance that if we need to change something, like adapting our data model to a new structure, we can do so easily and with minor impact. The obvious module choice here is Mongoose, which provides a layer of abstraction over the MongoDB driver.


#### Modules

Lets pick the right modules for the job.

In the [REST API Node Modules](http://develdoe.com/2016/rest-api-node-modules), I went over a list of modules that would help us achieve a pretty complete RESTful system; so let’s quickly pick some of them for this development:

* Restify will be the basis of everything we do. It’ll provide the structure needed to handle and process the requests and to provide a response to them.
* Swagger will be used to create the documentation. In the previous chapter, I talked about swagger-node-express, but just like that, there is one that works with Restify called (unsurprisingly enough) swagger-node-restify. This module was chosen because it integrates into our project, allowing us to autogenerate our documentation based on our original code, instead of having to maintain two different repositories.
* Halson will be our module of choice for adding hypermedia to our responses. Mainly chosen because it appears to be more mature than HAL (the other modules examined for this task).
* Finally, the validation of our JSONs will be done using TV4, mainly because it allows us to gather all validation errors at once.

*These are not the only modules that we’ll use; there are other minor auxiliary modules that will help us in different situations, but the ones listed are the ones that will help us achieve a RESTful API.*


## Summary

We now have all we need to start coding. We know the extent of the API for the bookstore chain that we’ll develop. We have planned the internal architecture of the system and have chosen the main modules that we’ll use.