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


