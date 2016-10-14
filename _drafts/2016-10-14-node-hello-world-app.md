---
date: '2016-10-14 11:52 +0200'
published: true
title: Node Hello World App
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