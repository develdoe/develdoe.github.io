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

## Statisk HTML & CSS

Börja med att clona mitt repo [DevelDevelStrap](https://github.com/DevelDoe/DevelDevelStap). Repot innehåller ett grundläggande html fil och tillhörande css filer. 

Modifiera sedan vår `html.index` efter de behov som vi har för vår applikation samt scripter för  de grundläggande applikationsberoende: jQuery, Underscore, Backbone.js, Handlebars och Backbone LocalStorage-adaptern.

```html
<!DOCTYPE html>
<html lang=en>
<head>
    <meta charset=utf-8>

    <title>DevelDevelStrap</title>
    <meta name=description content="Used for quickly strap up development projects.">
    <meta name=author content="DevelDoe">

    <meta http-equiv=X-UA-Compatable content="IE=edge,chrome=1">
    <meta name=viewport content="width=device-width, initial-scale=1.0">
    <link rel=stylesheet href="assets/css/reset.css"/>
    <link rel=stylesheet href="assets/css/fonts.css"/>
    <link rel=stylesheet href="assets/css/default.css">

    <!--[if lt IE 9]>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html5shiv/3.7.3/html5shiv.js"></script>
    <![endif]-->
</head>
<body id=background>
    <span id="todo-app">
        <header id=header>
            <h2>todos</h2>
        </header>
        <section id="content">
                <input id="new-todo" placeholder="What needs to be done?" autofocus>
                <label for="toggle-all">Mark all as completed</label>
                <input id="toggle-all" type="checkbox">
                <ul id="todo-list"></ul>
        </section>
        <footer id=footer>
            <h4>made by Develdoe</h4>
        </footer>               
    </span>

    <script src="lib/jquery.min.js"></script>
    <script src="lib/handlebars.min.js"></script>
    <script src="lib/underscore-min.js"></script>
    <script src="lib/backbone-min.js"></script>
    <script src="lib/backbone.localStorage.js"></script>

    <script src="models/Todo.js"></script>
    <script src="collections/Todos.js"></script>
    <script src="views/Todos.js"></script>
    <script src="views/AppView.js"></script>
    <script src="routers/router.js"></script>
    <script src="app.js"></script>
```

Förutom de ovan nämnda beroenden, notera att några andra applikationsspecifika filer också laddas. Dessa är organiserade i mappar som representerar deras ansvarsansvar: modeller, vyer, kollektioner och routrar. En app.js-fil är närvarande för att hämta centralinitieringskoden.

Obs! Om du vill följa med, skapa en katalogstruktur enligt vad som visas i index.html:
* Hämta jQuery, Underscore, Backbone, Handlebars och Backbone LocalStorage från sina respektive webbplatser och placera dem under lib/
* Skapa katalogerna models, collections, views, and routers

Du behöver också [index.css](https://github.com/log-andreeray/log-andreeray.github.io/blob/master/_posts/2017-11-16-css-development-stylesheet.md), som borde ligga i en assets katalog.

Vi kommer att skapa applikations JavaScript-filer under handledningen. Oroa dig inte för de två skriptelementen ‘text/template’ - vi kommer att ersätta dem snart!

### Application HTML

Låt oss nu fylla i index.html. Vi behöver en <input> för att skapa nya todos, en <ul id="todo-list" /> för att lista de faktiska todosna och en sidfot där vi senare kan infoga statistik och länkar för att utföra operationer som att rensa färdiga todos . Vi lägger till följande märkning omedelbart inuti vår body innan skriptelementen:

```html
<section id=todo-app>
    <header id=header>
        <h1>todos</h1>
        <input id="new-todo" placeholder="What needs to be done?" autofocus>
    </header>
    <section id="main">
        <input id="toggle-all" type="checkbox">
        <label for="toggle-all">Mark all as completed</label>
        <ul id="todo-list"></ul>
    </section>
    <footer id=footer>
    </footer>
</section>
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
        <strong>{{ remaining }}</strong>
        {{#if this.remaining === 1}} {{ this.item }} {{#else}} {{ this.items }} {{/if}} left
    </span>
    <ul id="filters">
        <li><a class=selected href=#>All</a>
        <li><a href=#/active>Active</a>
        <li><a href=#/completed>Completed</a>
    </ul>
    {{#if this.completed}} <button id="clear-completed">Clear Completed {{ this.completed }}</button> {{/if}}
</script>>
```

#stats-template visar antalet återstående ej completed objekt och innehåller en lista med hyperlänkar som kommer att användas för att utföra åtgärder när vi implementerar routern. Den innehåller också en knapp som kan användas för att rensa alla completed objekt.

Nu när vi har all HTML som vi behöver, börjar vi implementera vår applikation genom att återgå till grunden: en Todo-modell.

## Todo model

Todo-modellen är anmärkningsvärt enkel. För det första har en todo två attribut: en titel lagrar en todo-artikelens titel och en slutförd status indikerar om den är klar. Dessa attribut överförs som standard, enligt nedan:

```js
/**
 * models/Todo.js
 * --------------------
 */

var app = app || {}

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
 * collections/Todos.js
 * --------------------
 */

var app = app || {}

// Todos kollektion
// ----------------

// Kollektionen av todos stöds av * localStorage * istället för en fjärrserver.
var TodoList = Backbone.Collection.extend({

    // Referens till denna Kollektions modell.
    model: app.Todo,

    // Spara alla todo-objekten under `'todos-backbone`` namespace.
    localStorage: new Backbone.LocalStorage('todos-backbone'),

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


var obj = {
    num: 2,
    add: function() {
        this.add.call(this, this.num)
    }
}

function add (a) {
    return this.num + a
}

// ------------------

var obj = {
    num: 2
}

var add = function(a) {
    return this.num + a
}

add.call(obj, 3)
```

Kollektionens `completed()` och `remaining()` metoder returnerar en rad färdiga respektive oavslutade todos.

En `nextOrder()` metoden implementerar en sekvensgenerator medan en `comparator()` sorterar objekt genom sin införingsorder.

**Obs: `this.filter`, `this.without` and `this.last` är Underscore metoder som blandas in i Backbone.Collection så att läsaren vet hur man lär sig mer om dem.**

## Applikation Vy

Kärnapplikationslogiken som befinner sig i vyerna. Varje vy stöder funktionalitet som redigering, och innehåller därför en hel del logik.

För att hjälpa till att organisera denna logik använder vi element-kontroller modellen. Element-kontroller modellen består av två visningar: en styr en kollektion av objekt medan den andra hanterar varje enskilt objekt. 

I vårt fall kommer en AppView att hantera skapandet av nya todos och rendering av den ursprungliga todo-listan. Instanser av TodoView kommer att associeras med varje enskild Todo-post. Todo instanser kan hantera redigering, uppdatering och radera deras associerade todo.

För att hålla sakerna korta och enkla, kommer vi inte att implementera alla programmets funktioner i den här handledningen, vi täcker bara nog för att komma igång. Ändå finns det mycket för oss att täcka i AppView, så vi delar upp vår diskussion i två avsnitt.

```js
/**
 * views/App.js
 */

var app = app || {}

// Vår övergripande ** AppView ** är toppnivån av användargränssnittet.
app.AppView = Backbone.View.extend({

    // I stället för att skapa ett nytt element, binder vi till den befintligt #app
    el: '#todo-app',

    // Vår mall för statistiklinjen längst ner i appen.
    statsTemplate: Handlebars.compile($('#stats-template').html()),

    // Vid initialisering binder vi till relevanta händelser på 'Todos'
    // Kollektioner när objekt läggs till eller ändras.
    initialize: function() {
        this.allCheckBox = this.$('#toggle-all')[0]
        this.$input = this.$('#new-todo')
        this.$footer = this.$('#footer')
        this.$main = this.$('#main')

        this.listenTo(app.Todos, 'add', this.addOne)
        this.listenTo(app.Todos, 'reset', this.addAll)
    },

    // Lägg till ett enda todo-objekt i listan genom att skapa en vy för det och
    // lägga till dess element i `<ul>`.
    addOne: function(todo) {
        var view = new app.TodoView({ model: todo})
        $('#todo-list').append(view.render().el)
    },

    addAll: function() {
        this.$('#todo-list').html('')
        app.Todos.each(this.addOne, this)
    }
})												
```

Några anmärkningsvärda funktioner finns i vår första version av AppView, inklusive en `statsTemplate`, en initialize metod som implicit åberopas vid instantiering och flera vy specifika metoder.

Ett `el` (element) attribut lagrar en selektor som riktar in DOM-elementet med ett ID todoapp. När det gäller vår applikation hänvisar el till  elementet `<section id="todoapp">` i index.html.

Anropet till  Handlebars.compile använder Handlebars's templering för att konstruera ett `statsTemplate` objekt från vår #stats-template. Vi använder denna mall senare när vi gör vår vy.

Låt oss nu titta på initieringsfunktionen. För det första använder det jQuery för att cache de element som det kommer att använda till lokala egenskaper `(this.$()` finner element i förhållande till `this.$el`). Sedan binder vi två händelser på Todos kollektion: `add` och `reset`.

*Eftersom vi delegerar hantering av uppdateringar och deligeringar till TodoView-vyn behöver vi inte oroa oss för de här.*

De två delarna av logiken är:

* När en add händelse avfyras kallas metoden `addOne()` och tilldelas den nya modellen. `addOne()` skapar en instans av TodoView vy, renderar den och lägger till det resulterande elementet i vår Todo-lista.
* När en reset händelse inträffar (dvs uppdaterar vi kollektion i bulk som händer när Todos laddas från Local Storag), `addAll()` kallas, vilket itererar över alla Todos närvarande i vår kollektion och avfyrar `addOne()` för varje objekt.

**Observera att vi kunde använda this inom addAll() för att hänvisa till vyn eftersom listenTo() implicit ställer återropets sammanhang till vyn när den skapade bindningen.**

Nu, låt oss lägga till lite mer logik för att slutföra vår AppView:

```js
