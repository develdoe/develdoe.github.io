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

Först lägger vi upp header och de grundläggande applikationsberoende: jQuery, Underscore, Backbone.js, handlebars och Backbone LocalStorage-adaptern.

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
    <script src="lib/handlebars.min.js"></script>
    <script src="models/todo.js"></script>
    <script src="collections/todos.js"></script>
    <script src="views/todos.js"></script>
    <script src="views/app.ks"></script>
    <script src="routers/router.js"></script>
    <script src="app.js"></script>
```

Förutom de ovan nämnda beroenden, notera att några andra applikationsspecifika filer också laddas. Dessa är organiserade i mappar som representerar deras ansvarsansvar: modeller, vyer, kollektioner och routrar. En app.js-fil är närvarande för att hämta centralinitieringskoden.

Obs! Om du vill följa med, skapa en katalogstruktur enligt vad som visas i index.html:

* Placera index.html i en överordnad katalog.
* Hämta jQuery, Underscore, Backbone, Handlebars och Backbone LocalStorage från sina respektive webbplatser och placera dem under lib/
* Skapa katalogerna models, collections, views, and routers

Du behöver också [index.css](https://raw.githubusercontent.com/tastejs/todomvc/gh-pages/examples/backbone/node_modules/todomvc-app-css/index.css), som borde ligga i en assets katalog.

Vi kommer att skapa applikations JavaScript-filer under handledningen. Oroa dig inte för de två skriptelementen ‘text/template’ - vi kommer att ersätta dem snart!

### Application HTML

Låt oss nu fylla i index.html. Vi behöver en <input> för att skapa nya todos, en <ul id="todo-list" /> för att lista de faktiska todosna och en sidfot där vi senare kan infoga statistik och länkar för att utföra operationer som att rensa färdiga todos . Vi lägger till följande märkning omedelbart inuti vår body innan skriptelementen:

```html
<section id=todoapp>

    <header id=header>
        <h1>todos</h1>
        <input id="new-todo" placeholder="What needs to be done?" autofocus>
    </header>

    <section id="main">
        <input id="toggle-all" type="checkbox">
        <label for="toggle-all">Mark all as completed</label>
        <ul id="todo-list"></ul>
    </section>

    <footer id=footer></footer>

</section>

<div id="info">
    <p>Double-click to edit a todo</p>
    <p>Duplicated by <a href="log.andreeray.se">Andree Ray</a></p>
    <p>Part of <a href="http://todomvc.com">TodoMVC</a></p>
</div>
```

### Templates

För att slutföra index.html måste vi lägga till mallarna som vi ska använda för att dynamiskt skapa HTML genom att injicera modelldata i deras platshållare. Ett sätt att inkludera mallar på sidan är att använda egna skript taggar. Dessa utvärderas inte av webbläsaren, som bara tolkar dem som vanlig text. Handlebars templering kan sedan komma åt mallarna, vilket renderar fragment av HTML.

Vi börjar genom att fylla i  #item-template som används för att visa enskilda todo-objekt. 

```html
<script type="text/template" id="item-template">
    <div class="view">
        <input class="toggle" type="checkbox" {{#if competed}} checked="checked" {{/if}}>
        <label>{{title}}</label>
        <button class="destroy"></button>
    </div>
    <input class="edit" value="{{title}}">
</script>
```

Malltaggen i ovanstående markering, till exempel {{#if}}  och {{, är specifika för Handlebars.js och dokumenteras på handlebars-webbplatsen. I dina egna applikationer har du ett urval av mallbibliotek, t.ex. Mustache eller Underscore.

Vi måste också definiera  #stats-template som vi ska använda för att fylla i sidfoten.

```js
<script type="text/template" id="stats-template">
    <span id="todo-count">
        <strong>{{remaining}}</strong>
        {{#if this.remaining === 1}}
            {{this.item}}
        {{#else}}
            {{this.items}}
        {{/if}} left
    </span>
    <ul id="filters">
        <li><a class=selected href=#>All</a>
        <li><a href=#/active>Active</a>
        <li><a href=#/completed>Completed</a>
    </ul>
    {{#if this.completed}}
    <button id="clear-completed">Clear Completed {{this.completed}}</button>
    {{/if}}
</script>
```

#stats-template visar antalet återstående ej completed objekt och innehåller en lista med hyperlänkar som kommer att användas för att utföra åtgärder när vi implementerar routern. Den innehåller också en knapp som kan användas för att rensa alla completed objekt.

Nu när vi har all HTML som vi behöver, börjar vi implementera vår applikation genom att återgå till grunden: en Todo-modell.

## Todo model

Todo-modellen är anmärkningsvärt enkel. För det första har en todo två attribut: en titel lagrar en todo-artikelens titel och en slutförd status indikerar om den är klar. Dessa attribut överförs som standard, enligt nedan:

```js
var app = app || {}

/**
 * Todo model
 * -----------
 */

app.Todo = Backbone.Model.extend({

    defaults: {
        title: '',
        completed: false
    },

    // Toggle the 'completed' state of this todo item
    toggle: function() {
        this.save({ completed: !this.get('completed') })
    }
})
```

För det andra har Todo-modellen en `toggle()` metod genom vilken ett Todo-objektets slutförandestatus kan ställas in och samtidigt kvarstå.

## Todo Kollektion

Därefter används en TodoList-kollektion för att gruppera våra modeller. Kollektionen använder LocalStorage-adaptern för att åsidosätta Backbones `sync()` operation med en som kommer att lagra våra Todo-poster till HTML5 Local Storage. Genom lokal lagring sparas de mellan sidförfrågningar.

```js
/**
 * Todos.js
 */

var app = app || {}

// Todos kollektion
// ----------------

// Kollektionen av todos stöds av * localStorage * istället för en fjärrserver.
var TodoList = Backbone.Collection.extend({

    // Referens till denna Kollektions modell.
    model: app.Todo,

    // Spara alla todo-objekten under `'todos-backbone`` namespace.
    localStorage: new Backbone.localStorage('todos-backbone'),

    //Filtrera listan över alla todo-objekt som är färdiga.
    completed: function() {
        return this.filter(function(todo) {
            return todo.get('completed')
        })
    },

    // Filtrera ner listan för att bara lägga till objekt som fortfarande inte är färdiga.
    remaining: function() {
        return this.without.apply(this, this.completed)
    },

    // Vi håller Todos i sekventiell ordning, trots att de sparas av unordered
    // GUID i databasen. Detta genererar nästa ordernummer för nya objekt.
    nextOrder: function() {
        if(!this.length) return 1
        return this.last().get('order') + 1
    },

    // Todos sorteras efter deras ursprungliga införingsorder.
    comparator: function(todo) {
        return todo.get('order')
    }

})

// Skapa vår globala kollektion av ** Todos **.
var todos = new TodoList()
```

Kollektionens `completed()` och `remaining()` metoder returnerar en rad färdiga respektive oavslutade todos.

En `nextOrder()` metoden implementerar en sekvensgenerator medan en `comparator()` sorterar objekt genom sin införingsorder.

**Obs: this.filter, this.without and this.last är Underscore metoder som blandas in i Backbone.Collection så att läsaren vet hur man lär sig mer om dem.**


