---
date: '2016-10-14 16:32 +0200'
published: true
title: Node.js - Hello World first app
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
const http = require("http");
```

## Create Server

We call createServer() method to create a server instance. Pass it a anonymous function with request and response params and write a implementation that always return "Hello World".

Last we bind the server instans to a port using the listen() method. 

```javascript
const
    http = require("http"),
    hostname = '127.0.0.1',
    port = 3000,
    server = http.createServer(function(req,res){
        res.statusCode = 200
        res.setHeader('Content-Type','text/plain')
        res.end('Hello World\n')
    })

server.listen(port, hostname, function(){
    console.log('Sever running at http://'+hostname+':'+port)
})
```

Save the file as app.js and then execute the main.js to start the server as follows:

```bash
$ node app.js
```

Make a request at http://127.0.0.1:8081/ in any browser.
