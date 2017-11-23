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

Först lägger vi upp header och de grundläggande applikationsberoende: jQuery, Underscore, Backbone.js och Backbone LocalStorage-adaptern.

```html
<!doctype html>
<html lang="en">

<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
    <title>Backbone.js TodoMVC</title>
    <link rel="stylesheet" href="assets/index.css">
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
    <script src="views/app.js"></script>
    <script src="routers/router.js"></script>
    <script src="app.js"></script>
</body>

</html>
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
            <input class="toggle" type="checkbox" <%= completed ? 'checked' : '' %>>
            <label><%= title %></label>
            <button class="destroy"></button>
        </div>
        <input class="edit" value="<%= title %>">
</script>
```

Malltaggen i ovanstående markering, till exempel {{#if}}  och {{, är specifika för Handlebars.js och dokumenteras på handlebars-webbplatsen. I dina egna applikationer har du ett urval av mallbibliotek, t.ex. Mustache eller Underscore.

Vi måste också definiera  #stats-template som vi ska använda för att fylla i sidfoten lägg denna under den föregående.

```js
<script type="text/template" id="stats-template">
    <span id="todo-count"><strong><%= remaining %></strong> <%= remaining === 1 ? 'item' : 'items' %> left</span>
    <ul id="filters">
        <li>
            <a class="selected" href="#/">All</a>
        </li>
        <li>
            <a href="#/active">Active</a>
        </li>
        <li> 
            <a href="#/completed">Completed</a>
        </li>
    </ul>
    <% if (completed) { %>
        <button id="clear-completed">Clear completed (<%= completed %>)</button>
        <% } %>
</script>
```

#stats-template visar antalet återstående ej completed objekt och innehåller en lista med hyperlänkar som kommer att användas för att utföra åtgärder när vi implementerar routern. Den innehåller också en knapp som kan användas för att rensa alla completed objekt.

Nu när vi har all HTML som vi behöver, börjar vi implementera vår applikation genom att återgå till grunden: en Todo-modell.

## Todo model

Todo-modellen är anmärkningsvärt enkel. För det första har en todo två attribut: en titel lagrar en todo-artikelens titel och en slutförd status indikerar om den är klar. Dessa attribut överförs som standard, enligt nedan:

```js
// models/todo.js

var app = app || {};

// Todo Model
// ----------
// Vår grundläggande ** Todo ** -modell har `title` och` completed` attributter.

app.Todo = Backbone.Model.extend({

    // Standard attribut sörjer för att varje todo skapad har `title` och` completed` attribut.
    defaults: {
        title: '',
        completed: false
    },

    // Växla completed flaggan för detta todo-objekt.
    toggle: function() {
        this.save({
            completed: !this.get('completed')
        });
    }

});
```

För det andra har Todo-modellen en `toggle()` metod genom vilken ett Todo-objektets slutförandestatus kan ställas in och samtidigt kvarstå.

## Todo Kollektion

Därefter används en TodoList-kollektion för att gruppera våra modeller. Kollektionen använder LocalStorage-adaptern för att åsidosätta Backbones `sync()` operation med en som kommer att lagra våra Todo-poster till HTML5 Local Storage. Genom lokal lagring sparas de mellan sidförfrågningar.

```js
// collections/todos.js

var app = app || {};

// Todo Collection
// ---------------

// Kollektion av todos stöds av * localStorage * istället för en fjärrserver.
var TodoList = Backbone.Collection.extend({

    // Referens till denna samlings modell.
    model: app.Todo,

    // Spara alla todo-objekten under todos-backbone' namespace.
    localStorage: new Backbone.LocalStorage('todos-backbone'),

    // Filtrera listan över alla todo-objekt som är färdiga.
    completed: function() {
        return this.filter(function(todo) {
            return todo.get('completed');
        });
    },

    // Filtrera på listan för att bara lägga till objekt som fortfarande inte är färdiga.
    remaining: function() {
        return this.without.apply(this, this.completed());
    },

    // Vi håller Todos i sekventiell ordning,trots att de sparas av oordnad
    // GUID i databasen. Detta genererar nästa ordernummer för nya objekt.
    nextOrder: function() {
        if (!this.length) {
            return 1;
        }
        return this.last().get('order') + 1;
    },

    // Todos sorteras efter deras ursprungliga införingsorder.
    comparator: function(todo) {
        return todo.get('order');
    }
});

// Skapa vår globala samling ** Todos **.
app.Todos = new TodoList();
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
// views/app.js

var app = app || {};

// Applikationen
// ---------------

// Vår övergripande ** AppView ** är toppnivån av UI.
app.AppView = Backbone.View.extend({

    // Istället för att generera ett nytt element, bind till det befintliga skelettet av
    // Appen finns redan i HTML.
    el: '#todoapp',

    // Vår mall för statistiklinjen längst ner i appen.
    statsTemplate: _.template($('#stats-template').html()),

    // Vid initialisering binder vi till relevanta händelser på `Todos`
    // kollektion när objekt läggs till eller ändras.
    initialize: function() {
        this.allCheckbox = this.$('#toggle-all')[0];
        this.$input = this.$('#new-todo');
        this.$footer = this.$('#footer');
        this.$main = this.$('#main');

        this.listenTo(app.Todos, 'add', this.addOne);
        this.listenTo(app.Todos, 'reset', this.addAll);
    },

    // Lägg till ett enda todo-objekt i listan genom att skapa en vy för det och
    // lägger till sitt element i `<ul>`.
    addOne: function(todo) {
        var view = new app.TodoView({
            model: todo
        });
        $('#todo-list').append(view.render().el);
    },

    // Lägg till alla objekt i ** Todos ** -samlingen på en gång.
    addAll: function() {
        this.$('#todo-list').html('');
        app.Todos.each(this.addOne, this);
    }

});											
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
// views/app.js

var app = app || {};

// Applikationen
// ---------------

// Vår övergripande ** AppView ** är toppnivån av UI.
app.AppView = Backbone.View.extend({

    // Istället för att generera ett nytt element, bind till det befintliga skelettet av
    // Appen finns redan i HTML.
    el: '#todoapp',

    // Vår mall för statistiklinjen längst ner i appen.
    statsTemplate: _.template($('#stats-template').html()),

    // Ny
    // Delegerade händelser för att skapa nya objekt och rensa färdiga.
    events: {
        'keypress #new-todo': 'createOnEnter',
        'click #clear-completed': 'clearCompleted',
        'click #toggle-all': 'toggleAllComplete'
    },

    // Vid initialisering binder vi till relevanta händelser på `Todos`
    // kollektion när objekt läggs till eller ändras.
    initialize: function() {
        this.allCheckbox = this.$('#toggle-all')[0];
        this.$input = this.$('#new-todo');
        this.$footer = this.$('#footer');
        this.$main = this.$('#main');

        this.listenTo(app.Todos, 'add', this.addOne);
        this.listenTo(app.Todos, 'reset', this.addAll);

        // Ny
        this.listenTo(app.Todos, 'change:completed', this.filterOne);
        this.listenTo(app.Todos, 'filter', this.filterAll);
        this.listenTo(app.Todos, 'all', this.render);
    },

    // Ny
    // åter-rendering av App betyder bara att uppdatera statistiken - resten
    // av appen ändras inte.
    render: function() {
        var completed = app.Todos.completed().length;
        var remaining = app.Todos.remaining().length;

        if (app.Todos.length) {
            this.$main.show();
            this.$footer.show();

            this.$footer.html(this.statsTemplate({
                completed: completed,
                remaining: remaining
            }));

            this.$('#filters li a')
                .removeClass('selected')
                .filter('[href="#/' + (app.TodoFilter || '') + '"]')
                .addClass('selected');
        } else {
            this.$main.hide();
            this.$footer.hide();
        }

        this.allCheckbox.checked = !remaining;
    },

    // Lägg till ett enda todo-objekt i listan genom att skapa en vy för det och
    // lägger till sitt element i `<ul>`.
    addOne: function(todo) {
        var view = new app.TodoView({
            model: todo
        });
        $('#todo-list').append(view.render().el);
    },

    // Lägg till alla objekt i ** Todos ** -samlingen på en gång.
    addAll: function() {
        this.$('#todo-list').html('');
        app.Todos.each(this.addOne, this);
    },

    // Ny
    filterOne: function(todo) {
        todo.trigger('visible');
    },

    // Ny
    filterAll: function() {
        app.Todos.each(this.filterOne, this);
    },

    // NY
    // Generera attributen för ett nytt Todo-objekt.
    newAttributes: function() {
        return {
            title: this.$input.val().trim(),
            order: app.Todos.nextOrder(),
            completed: false
        };
    },

    // New
    // Om du enter i huvudinmatningsfältet skapar du en ny Todo-modell,
    // lagra det till localStorage.
    createOnEnter: function(event) {
        if (event.which !== ENTER_KEY || !this.$input.val().trim()) {
            return;
        }

        app.Todos.create(this.newAttributes());
        this.$input.val('');
    },

    // Ny
    // Rensa alla färdiga todo-objekt, förstör deras modeller.
    clearCompleted: function() {
        _.invoke(app.Todos.completed(), 'destroy');
        return false;
    },

    // Ny
    toggleAllComplete: function() {
        var completed = this.allCheckbox.checked;

        app.Todos.each(function(todo) {
            todo.save({
                'completed': completed
            });
        });
    }
});
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

var app = app || {};

// Todo objekt vy
// --------------

// DOM-elementet för ett todo-objekt...
app.TodoView = Backbone.View.extend({

    //... är en listetagg.
    tagName: 'li',

    // Cache mallfunktionen för ett enda objekt.
    template: _.template($('#item-template').html()),

    // DOM-händelserna specifika för ett objekt.
    events: {
        'dblclick label': 'edit',
        'keypress .edit': 'updateOnEnter',
        'blur .edit': 'close'
    },

    // TodoView lyssnar på ändringar i sin modell, åter-renderar. Eftersom det finns
    // en en-till-en korrespondens mellan en ** Todo ** och en ** TodoView **
    // sätter en direkt referens på modellen för enkelhets skull.
    initialize: function() {
        this.listenTo(this.model, 'change', this.render);
    },

    // Re-rendera titlarna på todo objekten.
    render: function() {
        this.$el.html(this.template(this.model.attributes));
        this.$input = this.$('.edit');
        return this;
    },

    // Växla denna vy till redigering läge, som visar inmatningsfältet.
    edit: function() {
        this.$el.addClass('editing');
        this.$input.focus();
    },

    // Stäng `"editing"`läge, spara ändringar i todo.
    close: function() {
        var value = this.$input.val().trim();

        if (value) {
            this.model.save({
                title: value
            });
        }

        this.$el.removeClass('editing');
    },

    // Om du trycker på `enter`, är vi färdiga med att redigera objektet.
    updateOnEnter: function(e) {
        if (e.which === ENTER_KEY) {
            this.close();
        }
    }
});
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


## Uppstart

Nu har vi två vyer, en AppView och en TodoView. Den förstnämnda behöver vara instanserad på sidbelastning så att dess kod körs. Detta kan åstadkommas genom jQuery's ready() util, som kommer att exekvera en funktion när DOM trädet är laddad.

```js
// app.js

var app = app || {};
var ENTER_KEY = 13;

$(function() {
    // Sparka igång saker genom att skapa ** App **.
    new app.AppView();
});
```

Låt oss pausa och se till att det arbete vi hittills har utfört fungerar som avsett.

Öppna filen: index.html i din webbläsare och övervaka konsolen. Om allt är bra ska du inte se några JavaScript-fel än vad gäller filen router.js som vi inte har skapat än.

Todo-listan ska vara tom eftersom vi ännu inte har skapat några todos. Dessutom finns det ytterligare arbete som vi behöver göra innan användargränssnittet fungerar fullt ut.

Några saker kan dock testas via JavaScript-konsolen.

Lägg till ett nytt todo-objekt i konsolen: `app.Todos.create({ title: 'My first Todo item' })`.

Om allt fungerar ordentligt, bör det logga den nya todo:n som vi just har lagt till i todos samlingen. Den nyskapade todo sparas också i Local Storage och kommer att finnas tillgänglig på siduppdatering.

`app.Todos.create()` exekverar en kollektione metod (`Collection.create(attributes, [options])`) som instanserar ett nytt modellobjekt av typen som passerat in i kollektionsdefinitionen, i vårt fall `app.Todo`:

```js
 var TodoList = Backbone.Collection.extend({

      model: app.Todo // the model type used by collection.create() to instantiate new model in the collection
      ...
  )};
```

Kör följande i konsolen för att kolla in det:

```js
var todo1 = app.Todos.create({ title : 'todo1' })
todo1 instanceof app.Todo // => true
```

De todos som läggs till via konsolen borde fortfarande visas i listan eftersom de är populerade från den Local Storage. Vi borde också kunna skapa en ny todo genom att skriva en titel och trycka på enter.

## Slutför och tar bort todos

Nästa del av vår handledning kommer att omfatta att slutföra och ta bort todos. Dessa två åtgärder är specifika för varje Todo-objekt, så vi måste lägga till den här funktionaliteten i TodoView-vyn.

```js
// views/app.js

var app = app || {};

// Applikationen
// ---------------

// Vår övergripande ** AppView ** är toppnivån av UI.
app.AppView = Backbone.View.extend({

    // Istället för att generera ett nytt element, bind till det befintliga skelettet av
    // Appen finns redan i HTML.
    el: '#todoapp',

    // Vår mall för statistiklinjen längst ner i appen.
    statsTemplate: _.template($('#stats-template').html()),

    // Ny
    // Delegerade händelser för att skapa nya objekt och rensa färdiga.
    events: {
        'keypress #new-todo': 'createOnEnter',
        'click #clear-completed': 'clearCompleted',
        'click #toggle-all': 'toggleAllComplete'
    },

    // Vid initialisering binder vi till relevanta händelser på `Todos`
    // kollektion när objekt läggs till eller ändras.
    initialize: function() {
        this.allCheckbox = this.$('#toggle-all')[0];
        this.$input = this.$('#new-todo');
        this.$footer = this.$('#footer');
        this.$main = this.$('#main');

        this.listenTo(app.Todos, 'add', this.addOne);
        this.listenTo(app.Todos, 'reset', this.addAll);

        // Ny
        this.listenTo(app.Todos, 'change:completed', this.filterOne);
        this.listenTo(app.Todos, 'filter', this.filterAll);
        this.listenTo(app.Todos, 'all', this.render);
    },

    // Ny
    // åter-rendering av App betyder bara att uppdatera statistiken - resten
    // av appen ändras inte.
    render: function() {
        var completed = app.Todos.completed().length;
        var remaining = app.Todos.remaining().length;

        if (app.Todos.length) {
            this.$main.show();
            this.$footer.show();

            this.$footer.html(this.statsTemplate({
                completed: completed,
                remaining: remaining
            }));

            this.$('#filters li a')
                .removeClass('selected')
                .filter('[href="#/' + (app.TodoFilter || '') + '"]')
                .addClass('selected');
        } else {
            this.$main.hide();
            this.$footer.hide();
        }

        this.allCheckbox.checked = !remaining;
    },

    // Lägg till ett enda todo-objekt i listan genom att skapa en vy för det och
    // lägger till sitt element i `<ul>`.
    addOne: function(todo) {
        var view = new app.TodoView({
            model: todo
        });
        $('#todo-list').append(view.render().el);
    },

    // Lägg till alla objekt i ** Todos ** -samlingen på en gång.
    addAll: function() {
        this.$('#todo-list').html('');
        app.Todos.each(this.addOne, this);
    },

    // Ny
    filterOne: function(todo) {
        todo.trigger('visible');
    },

    // Ny
    filterAll: function() {
        app.Todos.each(this.filterOne, this);
    },

    // NY
    // Generera attributen för ett nytt Todo-objekt.
    newAttributes: function() {
        return {
            title: this.$input.val().trim(),
            order: app.Todos.nextOrder(),
            completed: false
        };
    },

    // New
    // Om du enter i huvudinmatningsfältet skapar du en ny Todo-modell,
    // lagra det till localStorage.
    createOnEnter: function(event) {
        if (event.which !== ENTER_KEY || !this.$input.val().trim()) {
            return;
        }

        app.Todos.create(this.newAttributes());
        this.$input.val('');
    },

    // Ny
    // Rensa alla färdiga todo-objekt, förstör deras modeller.
    clearCompleted: function() {
        _.invoke(app.Todos.completed(), 'destroy');
        return false;
    },

    // Ny
    toggleAllComplete: function() {
        var completed = this.allCheckbox.checked;

        app.Todos.each(function(todo) {
            todo.save({
                'completed': completed
            });
        });
    }
});
```

Den viktigaste delen av detta är de två händelsehanterare som vi har lagt till, en togglecompleted händelse i todo's checkbox rutan och ett klickhändelse på todo-knappen `<button class="destroy" />`.

Låt oss titta på händelserna som uppstår när vi klickar på kryssrutan för ett todo-objekt:


1. Funktionen `togglecompleted()` åberopads som anropar `toggle()` på todo-modellen.
2. `toggle()` växlar den färdiga statusen för todo och anropar `save()` på modellen.
3. save skapar en `change` händelse på modellen som är bunden till vår TodoViews `render()` metod. Vi har lagt till ett uttryck i `render()` som växlar 'completed ' klassen på elementet beroende på modellens färdiga tillstånd. Den tillhörande CSS filen ändrar färgen på titeltexten och slår en linje genom den när todo är klar.
4.


