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

## The Specifications

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