---
date: '2017-11-14 12:51 +0100'
published: true
title: Backbone - Todos  app
---
*Den här artikeln är en översättning och försök till förenkling av kapitlet [Exercise 1: Todos - Your First Backbone.js App](https://addyosmani.com/backbone-fundamentals/#exercise-1-todos---your-first-backbone.js-app)*

Att bygga en Todo List är ett bra sätt att lära sig Backbone-konventioner. Det är en relativt enkel applikation, men tekniska utmaningar kring bindande, bestående modelldata, routing och templering ger möjligheter att illustrera vissa kärnfunktioner.

![todo app](https://addyosmani.com/backbone-fundamentals/img/todos_a.png)

låt oss överväga applikationens arkitektur på en hög nivå. Vi behöver:
* en Todo-modell för att beskriva enskilda todo-objekt
* en TodoList kollektion för att lagra och persistera todos
* ett sätt att skapa todos
* ett sätt att visa en lista över todos
* ett sätt att redigera befintliga todos
* ett sätt att markera en todo som färdigställd
* ett sätt att ta bort todos
* ett sätt att filtrera de objekt som har slutförts eller är kvar

I huvudsak är dessa funktioner klassiska CRUD-metoder. 

## Statisk HTML

Vi lägger hela vår HTML i en enda fil med namnet index.html.

## Header och Scripts

Först lägger vi upp header och de grundläggande applikationsberoende: jQuery, Underscore, Backbone.js och Backbone LocalStorage-adaptern.

```html
<!DOCTYPE html>
<html lang=en>
<head>
    <meta charset=utf-8>
    <meta http-equiv="X-UA-Compatable" content="IE=edge,chrome=1">
    <title>Backbone | todoMVC</title>
    <link rel="styelsheet" href="assets/index.css">
</head>
<body>
    <script type="text/template" id="item-template"></script>
    <script type="text/template" id="stats-template"></script>
    <script src="lib/jquery.min.js"></script>
    <script src="lib/underscore-min.js"></script>
    <script src="lib/backbone-min.js"></script>
    <script src="lib/backbone.localStorage.js"></script>
    <script src="models/todo.js"></script>
    <script src="collections/todos.js"></script>
    <script src="views/todos.js"></script>
    <script src="views/app.ks"></script>
    <script src="routers/router.js"></script>
    <script src="app.js"></script>
```

Förutom de ovan nämnda beroenden, notera att några andra applikationsspecifika filer också laddas. Dessa är organiserade i mappar som representerar deras ansvarsansvar: modeller, vyer, kollektioner och routrar. En app.js-fil är närvarande för att hämta centralinitieringskoden.