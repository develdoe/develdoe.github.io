---
date: '2016-12-13 13:46 +0100'
published: true
title: Node - Restful API with Express 4
category:
  - Node
---

**Prequsite**

You must have mongo installed:

* [Ubuntu - Install and start Monddb](/2016/ubuntu-mongo-db-ce/)
* [OSX - Install and start Monddb](/2016/osx-mongo-db/)

**package.json**

```json
{
  "name": "node-api",
  "main": "server.js",
  "dependencies": {
    "body-parser": "~1.0.1",
    "express": "~4.0.0",
    "mongoose": "^4.9.2"
  },
  "scripts": {
    "start": "supervisor server.js"
  }
}
```

**server.js**

```js
// SETUP
// ===============================================

var express     = require('express')
var app         = express()
var bodyParser  = require('body-parser')
var mongoose    = require('mongoose')
var User        = require('./app/models/user');

// configure bodyParser()
// this will let us get the data from a POST
app.use(bodyParser.urlencoded({ extended: true }))
app.use(bodyParser.json())

var port = process.env.PORT || 4000

// database
mongoose.connect('mongodb://localhost:27017/restapi');


// ROUTES
// ================================================
var router = express.Router()


// /api
router.get('/', function (req,res){
    res.json({message: 'welcome to the api!'})
})

// /restapi/users
// ------------------------------------------------
router.route('/users')

    .post(function(req, res) {

        var user = new User()

        user.name       = req.body.name
        user.email      = req.body.email
        user.password   = req.body.password


        user.save(function(err) {
            if (err) throw err;
            res.json({message:'User saved successfully!'});
        });

    })

    // get all the bears (accessed at GET http://localhost:8080/api/bears)
    .get(function(req, res) {
        User.find(function(err, users) {
            if (err) res.send(err);
            res.json(users);
        });
    })

// /restapi/users/:user_id
// -----------------------------------------------
router.route('/users/:user_id')

    .get(function (req,res) {
        User.findById(req.params.user_id, function (err, user) {
            if (err) res.send(err)
            res.json(user)
        })
    })

    .put(function (req,res) {
        User.findById(req.params.user_id, function (err, user) {
            if (err) res.send(err)
            user.name       = req.body.name
            user.email      = req.body.email
            user.password   = req.body.password
            user.save(function (err) {
                if (err) res.send(err)
                res.json({message: 'user updated'})
            })
        })
    })

    .delete(function (req, res) {
        User.remove({
            _id: req.params.user_id
        }, function (err, user) {
            if (err) res.send(err)
            res.json({message: 'Successfully deleted user'})
        })
    })


//  REGISER
//  ===============================================
// prefix /api
app.use('/restapi', router)

// START
// ================================================
app.listen(port)
console.log('Server upp on port', port)
```

**modules/user.js**

```js
 // grab the things we need
 var mongoose = require('mongoose');

 // create a schema
 var userSchema = new mongoose.Schema({
   name: String,
   email: { type: String, required: true, unique: true },
   password: { type: String, required: true },
   admin: Boolean,
   location: String,
   meta: {
     age: Number,
     website: String
   },
   created_at: Date,
   updated_at: Date
 });

 // make this available to our users in our Node applications
 module.exports = mongoose.model('User', userSchema);
```
