---
date: '2016-12-13 13:46 +0100'
published: true
title: Node - Restful API with Express 4
---

**Prequsite**

You must have mongo installed:

* [Ubuntu - Install and start Monddb](http://develdoe.com/2016/ubuntu-mongo-db-ce/) 
* [OSX - Install and start Monddb](http://develdoe.com/2016/osx-mongo-db/)

**package.json**

```bash
{
    "name": "starwaves",
    "main": "server.js",
    "dependencies": {
        "express": "~4.0.0",
        "mongoose": "~3.6.13",
        "body-parser": "~1.0.1"
    }
}
```

**server.js**

```bash
// file: server.js

// Base Setup
// =============================================================================


var express     = require('express')
var app         = express()
var bodyParser  = require('body-parser')

var mongoose = require('mongoose')
mongoose.connect('mongodb://localhost:27017/db')

// body parser will let us get data from a post
app.use(bodyParser.urlencode({extended:true}))
app.use(bodyParser.json())

var port = process.env.PORT || 8080

// Routes
// =============================================================================
var router = express.Router()

// test our routes. (accessed at GET http://localhost:8080/api)
router.get('/',function(req,res){
    res.json({message: 'response from api'})
})

// Register routes -------------------------------

app.use('/api', router)

// Boot
// =============================================================================
app.listen(port)
console.log('Magic on port: ' + port)
```

