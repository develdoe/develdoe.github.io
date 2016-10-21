---
date: '2016-10-19 13:44 +0200'
published: true
title: Node - REST API Mockup
---
In this article we'll build an REST API mock to use in development. It's going to be  a step by step guide and I am not going to ramble on about the specifics. Instead its a short and to the point guide on how to get an API backend to use while working on your front-end code of choise.

Create a directory:  
`$ mkdir mock_api` 

Navigate to it:  
`$ cd mock_api` 

Run npm init to setup our package.json *(You can leave all the default settings, or change them as you see fit.)*  
`$npm init`   

Run:  
`$ npm install express  --save` 

Creata a server file *server.js*

```javascript
var express         = require("express"),
    app             = express()

app.get('/movies',(req,res)=>{
    var movies = [
        {title : 'Suicide Squad', release: '2016', score: 8, reviewer: 'Robert Smith', publication : 'The Daily Reviewer'},
        {title : 'Batman vs. Superman', release : '2016', score: 6, reviewer: 'Chris Harris', publication : 'International Movie Critic'},
        {title : 'Captain America: Civil War', release: '2016', score: 9, reviewer: 'Janet Garcia', publication : 'MoviesNow'},
        {title : 'Deadpool', release: '2016', score: 9, reviewer: 'Andrew West', publication : 'MyNextReview'},
        {title : 'Avengers: Age of Ultron', release : '2015', score: 7, reviewer: 'Mindy Lee', publication: 'Movies n\' Games'},
        {title : 'Ant-Man', release: '2015', score: 8, reviewer: 'Martin Thomas', publication : 'TheOne'},
        {title : 'Guardians of the Galaxy', release : '2014', score: 10, reviewer: 'Anthony Miller', publication : 'ComicBookHero.com'},
    ]
    res.json(movies)
})

app.get('/reviewers',(req,res)=>{
    var authors = [
        {name : 'Robert Smith', publication : 'The Daily Reviewer', avatar: 'https://s3.amazonaws.com/uifaces/faces/twitter/angelcolberg/128.jpg'},
        {name: 'Chris Harris', publication : 'International Movie Critic', avatar: 'https://s3.amazonaws.com/uifaces/faces/twitter/bungiwan/128.jpg'},
        {name: 'Janet Garcia', publication : 'MoviesNow', avatar: 'https://s3.amazonaws.com/uifaces/faces/twitter/grrr_nl/128.jpg'},
        {name: 'Andrew West', publication : 'MyNextReview', avatar: 'https://s3.amazonaws.com/uifaces/faces/twitter/d00maz/128.jpg'},
        {name: 'Mindy Lee', publication: 'Movies n\' Games', avatar: 'https://s3.amazonaws.com/uifaces/faces/twitter/laurengray/128.jpg'},
        {name: 'Martin Thomas', publication : 'TheOne', avatar : 'https://s3.amazonaws.com/uifaces/faces/twitter/karsh/128.jpg'},
        {name: 'Anthony Miller', publication : 'ComicBookHero.com', avatar : 'https://s3.amazonaws.com/uifaces/faces/twitter/9lessons/128.jpg'}
    ]
    res.json(authors)
})

app.get('/publications',(req,res)=>{
    var publications = [
        {name : 'The Daily Reviewer', avatar: 'glyphicon-eye-open'},
        {name : 'International Movie Critic', avatar: 'glyphicon-fire'},
        {name : 'MoviesNow', avatar: 'glyphicon-time'},
        {name : 'MyNextReview', avatar: 'glyphicon-record'},
        {name : 'Movies n\' Games', avatar: 'glyphicon-heart-empty'},
        {name : 'TheOne', avatar : 'glyphicon-globe'},
        {name : 'ComicBookHero.com', avatar : 'glyphicon-flash'}
    ];
    res.json(publications)
})

app.get('/pending',(req,res)=>{
    var pending = [
        {title : 'Superman: Homecoming', release: '2017', score: 10, reviewer: 'Chris Harris', publication: 'International Movie Critic'},
        {title : 'Wonder Woman', release: '2017', score: 8, reviewer: 'Martin Thomas', publication : 'TheOne'},
        {title : 'Doctor Strange', release : '2016', score: 7, reviewer: 'Anthony Miller', publication : 'ComicBookHero.com'}
    ]
    res.json(pending)
})

app.listen(8080)
```

Run:  
`node server` 

Navigating:  
`localhost:8080/*`
