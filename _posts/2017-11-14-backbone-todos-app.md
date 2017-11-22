---
date: '2017-11-14 12:51 +0100'
published: true
title: Backbone - Todos  app
---
*Mycket av denna artikel är tagen från [Exercise 1: Todos - Your First Backbone.js App](https://addyosmani.com/backbone-fundamentals/#exercise-1-todos---your-first-backbone.js-app).*

Att bygga en Todo List är ett bra sätt att lära sig Backbone-konventioner. Det är en relativt enkel applikation, men tekniska utmaningar kring bindande, bestående modelldata, routing och templering ger möjligheter att illustrera vissa kärnfunktioner.

![todo app](https://addyosmani.com/backbone-fundamentals/img/todos_a.png)

låt oss se över  applikationens arkitektur på en hög nivå. Vi behöver:

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

Modifiera sedan vår `html.index` efter de behov som vi har för vår applikation samt lägga till de dependecies som applikationen är beroende av; jQuery, Underscore, Backbone.js, Handlebars och Backbone LocalStorage-adaptern.

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

Förutom de ovan nämnda beroenden, notera de andra applikationsspecifika. Dessa är organiserade i mappar som representerar deras ansvarsansvar: modeller, vyer, kollektioner och routrar. En app.js-fil är närvarande för centralinitieringskoden.

* Hämta jQuery, Underscore, Backbone, Handlebars och Backbone LocalStorage från sina respektive webbplatser och placera dem under lib/
* Skapa katalogerna models, collections, views, and routers

### Templates

För att slutföra index.html måste vi lägga till mallarna som vi ska använda för att dynamiskt skapa HTML genom att injicera modelldata i deras platshållare. Ett sätt att inkludera mallar på sidan är att använda egna skript taggar. Dessa utvärderas inte av webbläsaren, som bara tolkar dem som vanlig text. Handlebars compilerar sedan dessa och renderar fragment av dynamisk HTML.

Vi börjar genom att fylla i  #item-template som används för att visa enskilda todo-objekt längst ned ovan för #todo-app avslutande `</span>` node. 

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

Vi måste också definiera  #stats-template som vi ska använda för att fylla i sidfoten lägg denna under den föregående.

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

Ett `el` (element) attribut lagrar en selektor som riktar in DOM-elementet med ett ID todoapp. När det gäller vår applikation hänvisar el till  elementet `<span id="todo-app">` i index.html.

Anropet till  Handlebars.compile använder Handlebars's templering för att konstruera ett `statsTemplate` objekt från vår #stats-template. Vi använder denna mall senare när vi gör vår vy.

Låt oss nu titta på initieringsfunktionen. För det första använder det jQuery för att cache de element som det kommer att använda till lokala egenskaper `(this.$()` finner element i förhållande till `this.$el`). Sedan binder vi två händelser på Todos kollektion: `add` och `reset`.

*Eftersom vi delegerar hantering av uppdateringar och deligeringar till TodoView-vyn behöver vi inte oroa oss för de här.*

De två delarna av logiken är:

* När en add händelse avfyras kallas metoden `addOne()` och tilldelas den nya modellen. `addOne()` skapar en instans av TodoView vy, renderar den och lägger till det resulterande elementet i vår Todo-lista.
* När en reset händelse inträffar (dvs uppdaterar vi kollektion i bulk som händer när Todos laddas från Local Storag), `addAll()` kallas, vilket itererar över alla Todos närvarande i vår kollektion och avfyrar `addOne()` för varje objekt.

**Observera att vi kunde använda this inom `addAll()` för att hänvisa till vyn eftersom `listenTo()` implicit ställer återropets sammanhang till vyn när den skapade bindningen.**

Nu, låt oss lägga till lite mer logik för att slutföra vår AppView:

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

    // Delegerade händelser för att skapa nya objekt och rensa färdiga.
    events: {
        'keypress #new-todo' : 'createOnEnter',
        'click #clear-completed' : 'clearCompleted',
        'click #toggle-all' : 'toggleAllCompleted'
    },

    // Vid initialisering binder vi till relevanta händelser på 'Todos'
    // Kollektioner när objekt läggs till eller ändras. Starta saker genom att
    // ladda några redan existerande todos som kan finnas sparas i localStorage.
    initialize: function() {
        this.$main = this.$('#main')
        this.$input = this.$('#new-todo')
        this.$footer = this.$('#footer')
        this.allCheckbox = this.$('#toggle-all')[0]

        this.listenTo(app.Todos, 'add', this.addOne)
        this.listenTo(app.Todos, 'reset', this.addAll)

        this.listenTo(app.Todos, 'change:completed', this.filterOne)
        this.listenTo(app.Todos, 'filter', this.filterAll)
        this.listenTo(app.Todos, 'all', this.render)

        app.Todos.fetch()
    },

    // rendering gör så man bara uppdaterar statistiken - resten
    // av appen ändras inte.
    render: function() {
        var completed = app.Todos.completed().length
        var remaining = app.Todos.remaining().length

        if(app.Todos.length) {
            this.$main.show()
            this.$footer.show()

            this.$footer.html(this.statsTemplate({
                completed: completed,
                remaining: remaining
            }))

            this.$('#filters li a')
                .removeClass('selected')
                .filter('[href=#/]' + (app.TodoFiler ||  '' ) + '"]"')
                .addClass('selected')
        } else {
            this.$main.hide()
            this.$footer.hide()
        }

        this.allCheckbox.checked = !remaining
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
    },

    filterOne: function() {
        todo.trigger('visible')
    },

    filterAll: function() {
        app.Todos.each(this.filterOne, this)
    }

    // Generera attributen för ett nytt Todo-objekt.
    newAttribute: function() {
        return {
            title: this.input.val().trim(),
            order: app.Todos.nextOrder(),
            completed: false
        }
    }

    // Om du trycker på enter i huvudinmatningsfältet, skapa en ny Todo-modell,
    // för att behålla den till localStorage.
    createOnEnter: function(e) {
        if (e.which !== ENTER_KEY || !this.$input.val().trim()) {
            return;
        }
        app.Todos.create(this.newAttribute())
        this.$input.val('')
    },

    // Rensa alla färdiga todo-objekt, förstöra deras modeller.
    clearCompleted: function() {
        _.invoke(app.Todos.completed(), 'destroy')
        return false
    },

    toggleAllCompleted: function() {
        var completed = this.allCheckbox.checked

        app.Todos.each(function(todo) {
            todo.save({
                'completed':completed
            })
        })
    }

})
```

Vi har lagt till logiken för att skapa nya todos, redigera dem och filtrera dem baserat på deras slutförda status.

* händelser: Vi har definierat en händelsehash som innehåller deklarativa callbacks för våra DOM-händelser. Det binder dessa händelser till följande metoder:
* `createOnEnter()`: Skapar en ny Todo-modell och behåller den i localStorage när en användare tycker på enter in i fältet `<input />`. Återställer också det huvudsakliga `<input />` -fältet för att förbereda det för nästa post. Modellen är populerad av `newAttributes()`, som returnerar ett objekt bokstavligt sammansatt av title, order och completed tillstånd för det nya objektet. Observera att det här hänvisar till vyn och inte DOM-elementet eftersom callback var bunden med händelseshashen.
* `clearCompleted()`: Tar bort objekten i todo-listan som har markerats som slutförda när användaren klickar på kryssrutan clear-completed (den här kryssrutan kommer att finnas i sidfoten som fylls i av #stats-template).
* `toggleAllComplete()`: Tillåter att en användare markerar alla objekt i todo-listan som färdigställd genom att klicka på kryssrutan toggle-all.
* `initialize()`: Vi har bundit callbacks till flera ytterligare händelser:
* Vi har bundit ett `filterOne()` callback på Todos kollektion för en `change:completed` händelse. Detta lyssnar på ändringar i den completed flaggan för alla modell i samlingen. Den drabbade todo skickas till callbacken som utlöser en anpassad synlig händelse på modellen.
* Vi har bundit ett `filterAll()` callback för en filter-händelse, som fungerar lite som `addOne()` och `addAll()`. Dess ansvar är att växla vilka todo-objekt som är synliga utifrån det filter som för närvarande är valt i användargränssnittet (all, completed eller remaining) via anrop till `filterOne()`.
* Vi har använt det speciella all event för att binda alla händelser som utlöses på Todos-samlingen till renderingsmetod(diskuteras nedan).

Metoden `initialize()` färdigställs genom att hämta de tidigare sparade todos från localStorage.

`render()`: Flera saker händer i vår `render()` metod:
* `#main` och `#footer` sektionerna visas eller döljs beroende på om det finns några todos i samlingen.
* Footer är populerad med HTML som produceras genom att instansera statsTemplate med antalet färdiga och återstående todo-poster.
* HTML som produceras av föregående steg innehåller en lista över filterlänkar. Värdet av `app.TodoFilter`, som kommer att ställas in av vår router, används för att tillämpa klassen `"selected"` till länken som motsvarar det aktuella valda filteret. Detta leder till att villkorlig CSS-styling appliceras på det filtret.
* `allCheckbox` uppdateras baserat på om det finns resterande todos.

## Individuell Todo Vy

Låt oss nu titta på `TodoView` vyn. Denna kommer att ansvara för enskilda Todo poster, se till att vyn uppdateras när en todo gör det.

För att aktivera denna funktionalitet lägger vi till händelselyssare i den vy som lyssnar på händelser på en enskild todo HTML-representation.

```js
// views/todos.js

/**
 * Todo item view
 */

var app = app || {}

// DOM-elementet för ett todo-objekt ...
app.TodoView.Backbone.View.extend({

    // ... är en listetagg.
    tagName: 'li',

    // Cache the template function for a single item.
    // lagra  mallfunktionen för ett enda objekt.
    template: Handlebars.compile($('#item-template').html()),

    // DOM-händelserna specifika för ett objekt.
    events: {
        'dblclick label' : 'edit',
        'keypress .edit' : 'updateOnEnter',
        'blur .edit' : 'close'
    },

    // TodoView lyssnar på ändringar i sin modell, åter-renderar. Eftersom det finns
    // en en-till-en-korrespondens mellan en ** Todo ** och en ** TodoView ** i denna
    // app, sätter vi en direkt referens på modellen för enkelhets skull.
    initialize : function() {
        this.listenTo(this.model, 'change', this.render)
    },

    // åter-renderar titlarna för todo-objektet.
    render: function() {
        this.$el.html(this.template(this.model.attributes))
        this.$input = this.$('.edit')
        return this
    },

    // Växla denna vy till 'editing' läge, och visa inmatningsfältet.
    edit: function() {
        this.$el.addClass('edeting')
        this.$input.focus()
    },

    // Close the `"editing"` mode, saving changes to the todo.
    // Stäng editing läget, spara ändringar i todo.
    close: function() {
        var value = this.$input.valu().trim()
        if (value) this.model.save({ title:value })
        this.$el.removeClass('edeting')
    },

    // If you hit `enter`, we're through editing the item.
    // Om du slår `enter`, är man färdig med redigeringen
    updateOnEnter: function(e) {
        if (e.which === ENTER_KEY) this.close()
    }
})
```

I `initialize()` konstruerar vi en lyssnare som övervakar en todo-modells förändringshändelse. Till följd av det, när en todo blir uppdaterad kommer applikationen att åter-rendera och visuellt reflektera dess ändringar.

*Observera att modellen som passerat i argumentets hash av vår AppView är automatiskt tillgänglig för oss som this.model.*

I metoden `render()`, renderar vi vår Handlebars vi vår `#item-template`, som tidigare compilerades in i this.template med hjälp av Handlebars.compile metod.

Detta returnerar ett HTML-fragment som ersätter innehållet i visningselementet (ett li-element skapades implicit för oss baserat på egenskapen tagName).

Med andra ord är den renderade mallen nu närvarande under `this.el` och kan bifogas todo-listan i användargränssnittet. `render()` avslutar genom att cache inmatningselementet i den instantierade mallen i `this.$input`.

Våra händelser hash innehåller tre callbacks:

* `edit()`: Ändrar aktuell vy till redigeringsläget när en användare dubbelklickar på ett befintligt objekt i todo-listan. Detta gör det möjligt för dem att ändra det befintliga värdet av objektets titelattribut.
* `updateOnEnter()`: Kontrollerar att användaren har tryckt på Retur / Enter-tangenten och utför funktionen `close()`.
* `close()`: trimmar värdet av den nuvarande texten i vårt `<input />` -fält, så att vi inte behandlar det vidare om det inte innehåller någon text (t ex ''). Om ett giltigt värde har tillhandahållits lagrar vi ändringarna till nuvarande todo-modellen och stänger redigeringsläget genom att ta bort motsvarande CSS-klass.






