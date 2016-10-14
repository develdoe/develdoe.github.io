---
date: '2016-10-14 11:52 +0200'
published: true
title: Node Hello World App
category:
  - node
---
A Node.js application consists of the following three vital components:

* **Import required modules** − Require directive to load Node modules.
* **Create server** − A server which listen to client's requests.
* **Read request and response** − The server will read the HTTP request by a client.

## Import Module
We use the require directive to load the http module and store the returned HTTP instance into an http variable:

```javascript
var http = require("http");
```

## Create Server

We call createServer() method to create a server instance and then we bind it to a  port using the listen() method. 

Pass it a anonymous function with request and response params and write a implementation that always return "Hello World".

```javascript
var http = require("http")

http.createServer(function (request, response) {

   // Send the HTTP header, Status: 200 and Content Type: text/plain
   response.writeHead(200, {'Content-Type': 'text/plain'})
   
   // Send the response body as "Hello World"
   response.end('Hello World\n')
}).listen(8081)

// Print server and port to the console
console.log('Server running at http://127.0.0.1:8081/')
```

Save the file as app.js

##  Request & Response

Now execute the main.js to start the server as follows:

```bash
$ node main.js
```

Make a request to http://127.0.0.1:8081/ in any browser and observe the result.