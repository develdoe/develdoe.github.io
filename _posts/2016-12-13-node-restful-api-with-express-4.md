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

```js
// SETUP
// ===============================================

var express     = require('express')
var app         = express()
var bodyParser  = require('body-parser')
var mongoose    = require('mongoose')
var Test        = require('./models/test')

// configure bodyParser()
// this will let us get the data from a POST
app.use(bodyParser.urlencoded({ extended: true }))
app.use(bodyParser.json())

var port = process.env.PORT || 4000

// database
mongoose.connect('mongodb://localhost:27017/restApi')

// ROUTES
// ================================================
var router = express.Router()

// middleware to use for all requests
router.use(function(req,res,next){
    // TODO logging
    console.log(req.body.name)
    next()  // go to next
})

// /api
router.get('/', function (req,res){
    res.json({message: 'welcome to the api!'})
})

// /api/tests
// ------------------------------------------------
router.route('/tests')

    .post(function (req,res) {

        var test = new Test()
        test.str = req.body.name
        
        test.save(function(err){
            if(err) res.send(err)
            res.json({message: 'test created'})
        })
    })

    .get(function(req,res){
        Test.find(function (err, tests) {
            if (err) res.send(err)
            res.json(tests)
        })
    })


//  REGISER
//  ===============================================
// prefix /api
app.use('/api', router)

// START
// ================================================
app.listen(port)
console.log('Server upp on port', port)
```

