---
date: '2016-12-13 13:46 +0100'
published: true
title: Node - Restful API with Express 4
category:
  - Node
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

```js
// file: server.js

// Base Setup
// =============================================================================

var express     = require('express')
var app         = express()
var bodyParser  = require('body-parser')
var Bear        = require('./app/models/bear')

// Database connection
// ----------------------------------------------------

var mongoose = require('mongoose')
mongoose.connect('mongodb://localhost:27017/db')

// body parser will let us get data from a post
// ----------------------------------------------------

app.use(bodyParser.urlencoded({extended:true}))
app.use(bodyParser.json())

// Setup
// ----------------------------------------------------

var port = process.env.PORT || 8080

// Routes
// =============================================================================
var router = express.Router()

// middleware
// ----------------------------------------------------

app.use(function(req,res,next){
    console.log('hitting the api')
    next()
})

// ----------------------------------------------------

router.get('/',function(req,res){
    res.json({message: 'response from api'})
})

// /api/bears

router.route('/bears')

    .post(function(req,res){
            var bear = new Bear()
            bear.name = req.body.name

            bear.save(function(err){
                if(err) res.send(err)
                res.json({message: 'bear created'})
            })
    })


// Register routes
// ----------------------------------------------------

app.use('/api', router)

// Boot
// =============================================================================
app.listen(port)
console.log('Magic on port: ' + port)
```

**/app/models/bear.js**

```js
