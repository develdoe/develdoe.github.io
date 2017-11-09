---
date: '2017-10-30 14:54 +0100'
published: true
title: BackboneJS - Basics
---
I det här avsnittet lär du dig det grundläggande om Backbone-modeller, vyer, kollektioner, händelser och routrar. Detta är inte på något sätt en ersättning för den officiella dokumentationen, men det hjälper dig att förstå många av de grundläggande begreppen bakom Backbone innan du börjar bygga applikationer som använder den.

Innan vi dyker in i fler kodexemplar, låt oss definiera en viss boilerplattform som du kan använda för att ange vilka beroenden som Backbone kräver. Denna boilerplate kan återanvändas på många sätt med liten eller ingen ändring och låter dig köra kod från exempel med lätthet.

Du kan klistra in följande i din textredigerare, och ersätta kommentaren mellan skriptet med JavaScript från ett visst exempel:

```html
<!DOCTYPE HTML>
<html>
<head>
    <meta charset="UTF-8">
    <title>Backbone Boilerplate</title>
</head>
<body>

<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js"></script>
<script src="http://underscorejs.org/underscore-min.js"></script>
<script src="http://backbonejs.org/backbone-min.js"></script>
<script>
  // Your code goes here
</script>
```
Du kan sedan spara och köra filen i din webbläsare, t.ex. Chrome eller Firefox. Alternativt, om du föredrar att arbeta med en online-kodredigerare, är jsFiddle och jsBin-versioner av den här kedjan också tillgängliga.

De flesta exemplen kan också köras direkt från konsolen i webbläsarens utvecklingsverktyg, förutsatt att du har laddat HTML-sidan med kedjeplattformen så att Backbone och dess beroenden är tillgängliga för användning.

För Chrome kan du öppna DevTools via Chrome-menyn högst upp till höger: välj "Fler verktyg> verktyg för programmerare (eller F12)" eller alternativt använda genvägsknappen Control + Shift + I på Windows/Linux eller Kommando+Alternativ+I på Mac.

![devtools](https://addyosmani.com/backbone-fundamentals/img/devtools.png)

Därefter växlar du till fliken Console, varifrån du kan skriva in och köra all typ av JavaScript-kod genom att trycka på returnyckeln. Du kan också använda konsolen som en flertalsredigerare med hjälp av Shift + Enter-genvägen på Windows/Linux eller Ctrl+Enter genväg på Mac för att flytta från slutet av en rad till början på en annan.

## Modeller

Backbone modeller innehåller data för en applikation samt logiken kring dessa data. Till exempel kan vi använda en modell för att representera begreppet för ett todo-objekt, inklusive dess attribut som titel (todo innehåll) och färdigställt (nuvarande tillstånd för todo).

Modeller kan skapas genom att förlänga `Backbone.Model` enligt följande:

```js
var Todo = Backbone.Model.extend({})

// Vi kan sedan skapa vår egen konkreta instans av en (Todo) modell
// med inga värden alls:
var todo1 = new Todo()

// Följande loggar: {}
console.log(JSON.stringify(todo1))

// eller med några godtyckliga data:
var todo2 = new Todo({
    title: 'I am the title of todo2',
    completed: true
})

// Följande loggar: {"title": "I am the title of todo2", "Completed": true}
console.log(JSON.stringify(todo2))
```

#### Initiering

Metoden `initialize()` kallas när en ny instans av en modell skapas. Dess användning är valfri; men du kan se ett exempel nedan.


```js
var Todo = Backbone.Model.extend({
  initialize: function {
    console.log('Todo modellen har initierats.')
  }
});

var myTodo = new Todo();
// Loggar: Todo modellen har initierats.
```

### Standard attributvärden

Det finns tillfällen då du vill att din modell ska ha en uppsättning standardvärden (t ex i ett scenario där en komplett uppsättning data inte tillhandahålls av användaren). Detta kan ställas in med en egenskap som kallas defaults i din modell.

```js
var Todo = Backbone.Model.extend({
  initialize: function {
    console.log('Todo modellen har initierats.')
  },
  // Standard todo attributvärden
  defaults: {
    title: 'Todo Title',
    completed: false
  }
})

var todo1 = new Todo()

console.log(JSON.stringify(todo1)) // => {"Todo Title", "Completed": false}

// eller med några godtyckliga data:
var todo2 = new Todo({
    title: 'I am the title of todo2',
    completed: true
})

console.log(JSON.stringify(todo2)) // => {"title": "I am the title of todo2", "Completed": true}
```

#### Getters & Setters
### Model.get()

`Model.get()` ger enkel åtkomst till en modells attribut.

```js
var Todo = Backbone.Model.extend({
    initialize: function () {
        console.log('Todo modellen har initierats.')
    },
    defaults: {
        title: 'Todo Title',
        completed: false
    }
})

var todo1 = new Todo()
console.log(todo1.get('title')) // => Todo Title
console.log(todo1.get('completed')) // => false
console.log(JSON.stringify(todo1)) // => {"Todo Title", "Completed": false}

// eller med några godtyckliga data:
var todo2 = new Todo({
    title: 'I am the title of todo2',
    completed: true
})

console.log(JSON.stringify(todo2)) // => {"title": "I am the title of todo2", "Completed": true}
```

Om du behöver läsa eller klona alla modelldatatribut, använd dess metod `toJSON()`. Denna metod returnerar en kopia av attributen som ett objekt (inte EN JSON sträng trots sitt namn). (När `JSON.stringify()` skickas ett objekt med en `toJSON()` metod, strängar det returneringsvärdet för `toJSON()` istället för det ursprungliga objektet. Exemplen i föregående avsnitt utnyttjade denna funktion när de kallade `JSON.stringify()` för att logga på modellinstanserna.)


### Model.set()

`Model.set()` anger en hash som innehåller en eller flera attribut på modellen. När någon av dessa attribut förändrar modellens tillstånd, utlöses en "change" händelse på den. change händelser för varje attribut utlöses också och kan vara bundna till (t.ex. `change:name`, `change:age`).

```js
var Todo = Backbone.Model.extend({
    initialize: function () {
        console.log('Initialize:')
    },
    defaults: {
        title: 'Todo Title',
        completed: false
    }
})

// Ange värdet av attribut via instantiering
var myTodo = new Todo({
    title: 'Angivet genom instantiering.'
})
console.log('Todo title: ' + myTodo.get('title'))       // => Angivet genom instantiering.
console.log('completed: ' + myTodo.get('completed'))    // => false (Angivet genom defaults)

// Ange ett enkelt attributvärde en åt gången genom Model.set():
myTodo.set('title', 'Title attribut angivet genom Model.set()')
console.log('Todo title' + myTodo.get('title'))         // => Angivet genom Model.set()
console.log('completed: ' + myTodo.get('completed'))    // => false (Angivet genom defaults)

// Ange karta över attribut via Model.set ():
myTodo.set({
    title: 'Both attributes set through Model.set()',
    completed: true
})
console.log('Todo title' + myTodo.get('title'))         // => Both attributes set through Model.set()
console.log('completed: ' + myTodo.get('completed'))    // => true
```

### Direkt tillgång

Modeller exponerar ett `.attributes` för attribut som representerar en intern hash som innehåller tillståndet för den modellen. Detta är i allmänhet i form av ett JSON-objekt som liknar modelldata som du kan hitta på servern men kan ta andra former.

Genom att ställa in värden genom `.attribures` attributet på en modell kringgår utlösare som är bundna till modellen.

Passerar `{silent:true}` på set försenar inte enskilda `"change:attr"` händelser. Istället är de tystade helt:

```js
var Person = new Backbone.Model()

Person.on("change:name", function() {
    console.log('Name changed')
})

Person.set({name: 'Andree'})                // => Name Changed
Person.set({name: 'Ray'}, {silent: true})   // =>

console.log(Person.hasChanged('name'))       // => true
console.log(Person.hasChanged(null))         // => true
```

*Kom ihåg om det är möjligt är det bästa sättet att använda Model.set (), eller direkt instansiering som förklarats tidigare.*

#### Lyssna på ändringar i din modell

Om du vill få en notice när en Backbone modell ändras kan du binda en lyssnare till modellen för dess förändringshändelse. En praktisk plats för att lägga till lyssnare är i `initialize()` metoden som visas nedan:

```js
var Todo = Backbone.Model.extend({
    defaults: {
        title: '',
        completed: false
    },
    initialize: function () {
        console.log('Initierat Todo modellen')
        this.on('change', function() {
            console.log('Värdena för denna modell har ändrats.')
        })
    }
})

var myTodo = new Todo()

myTodo.set('title', 'Lyssnaren utlöses när ett attributvärde ändras.')
console.log('Title har ändrats: ' + myTodo.get('title'))

myTodo.set('completed', true)
console.log('Completed har ändrats: ' + myTodo.get('completed'))

myTodo.set({
  title: 'Att ändra mer än ett attribut samtidigt utlöser bara lyssnaren en gång.',
  completed: true
})
```

Du kan också lyssna på ändringar av enskilda attribut i en Backbone modell. I det följande exemplet loggar vi ett meddelande när ett specifikt attribut (titeln på vår Todo-modell) ändras.

```js
var Todo = Backbone.Model.extend({
    defaults: {
        title: '',
        completed: false
    },

    initialize: function () {
        console.log('Initierat Todo modellen')
        this.on('change:title', function() {
            console.log('Title värdet för denna modellen har ändrats.')
        })
    },

    setTitle: function(newTitle) {
        this.set({title:newTitle})
    }
})

var myTodo = new Todo()

myTodo.set('title', 'A Title')
myTodo.setTitle('This title is from .setTitle function ')

myTodo.set('completed', true)
console.log('Todo set as completed: ', myTodo.get('completed'))
```

#### Validering

Backbone stöder modellvalidering genom model.validate(), som tillåter att kontrollera attributvärdena för en modell innan de ställs in.

Som standard sker validering när modellen förbehålls med metoden `save()` eller när `set()` åberopas om `{validate:true}` skickas som argument.

```js
var Person = new Backbone.Model({name: 'Ray'})

// Validera model namnet
Person.validates = function(attrs) {
    if(!attrs.name) {
        return 'I need your name!'
    }
}

Person.set('name', 'Samuel')
console.log(Person.get('name'))

Person.unset('name', {validate:true})
```

Ovan använder vi också metoden `unset()`, som tar bort ett attribut genom att radera det från den interna modellattributets hash.

Valideringsfunktionerna kan vara så enkla eller komplexa som nödvändigt. Om de angivna attributen är giltiga, ska inget returneras från .validate (). Om de är ogiltiga ska ett felvärde returneras istället.

Skulle ett fel returneras:

* En `invalid` händelse kommer att utlösas, som anger `validationError` egenskapen på modellen med det värde som returneras med den här metoden.
* `.save()` fortsätter inte och attributen på modellen kommer inte att ändras på servern.

```js
var Todo = Backbone.Model.extend({

    defaults: {
        completed: false
    },

    validate: function(attributes) {
        if(attributes.title === undefined) {
            return 'Du måste ange en titel för din todo'
        }
    },

    initialize: function() {
        console.log('initialized')
        this.on('invalid', function(model,error) {
            console.log(error)
        })
    }
})

var myTodo = new Todo()
myTodo.set('completed', true, {validate: true})
console.log('completed: ' + myTodo.get('completed'))
```

**Obs! `attributes` objeket som skickats till `validate` metoden representerar vad attributen skulle vara efter att ha slutfört den aktuella set() eller save(). Detta objekt skiljer sig från modellens aktuella attribut och från parametrar som överförs till operationen. Eftersom det är skapat av utliga kopior, är det inte möjligt att ändra Number, String eller Booleskt attribut för inmatningen i funktionen, men det är möjligt att ändra attribut i nestade objekt.**

Ett exempel på detta finns som [jsfiddle example](http://jsfiddle.net/2NdDY/270/).

Observera också att validering vid initialisering är möjlig men med begränsad användning, eftersom objektet som konstrueras är internt märkt ogiltigt men ändå vidarebefordrat till den som åberoparen (fortsätter ovanstående exempel):

```js
var emptyTodo = new Todo(null, {validate: true})
console.log(emptyTodo.validationError);
```

## Vyer

Visningar i Backbone innehåller inte HTML-markup för din applikation. De innehåller logiken bakom presentation av modellens data till användaren. Detta uppnås vanligtvis med hjälp av JavaScript-templering (t.ex. Underscore Microtemplates, Mustache, jQuery-tmpl, etc.). 

En vys för `render()` metoden kan vara bunden till en modells `change()` händelse, vilket möjliggör att vyn omedelbart återspeglar modelländringar utan att kräva fullständig siduppdatering.

### Skapa nya vyer

Att skapa en ny vy är relativt enkel och liknar att skapa nya modeller. För att skapa en ny vy, bara utöka Backbone.View. Vi introducerade exemplet TodoView nedan i föregående kapitel; Låt oss nu ta en närmare titt på hur det fungerar:

```js
var TodoView = Backbone.View.extend({

    tagName: 'li',

    // Cache template funktionen för ett enda objekt.
    todoTpl: _.template("template"),

    events: {
        'dblclick label': 'edit',
        'keypress .edit': 'updateOnEnter',
        'blur .edit': 'close'
    },

    initialize: function() {
        var args = (arguments.length === 1 ? [arguments[0]] : Array.apply(null, arguments))
        var options = args.pop()
        // I Backbone 1.1.0, om du vill komma åt godkända alternativ i
        // din vy, du måste spara dem enligt följande:
        this.options = options || {}
    },

    // åter-rendera titeln på todo-objektet.
    render: function() {
        this.$el.html(this.todoTpl(this.model.attributes))
        this.input = this.$('.edit')
        return this
    },

    edit: function() {
        // exekveras när todo-etiketten är dubbelklickad
    },

    updateOnEnter: function() {
        // exekveras på varje knapptryckning du gör det i redigeringsläge,
        // men vi väntar på att komma in för att komma igång
    },

    close: function() {
        // exekveras när todo förlorar fokus
    },

})

var todoView = new TodoView()

// log refererar till ett DOM-element som motsvarar vyns instans
console.log(todoView.el) // logs <li></li>
```

#### Vad är `el`?

Den centrala egenskapen för en vy är `el` (värdet loggat i det sista uttrycket i exemplet). Vad är `el` och hur är det definierat?

`el` är i grunden en referens till ett DOM-element och alla vyer måste ha en.

Visningar kan använda el för att komponera dess elements innehåll och sedan infoga det i DOM trädet på en gång, vilket gör det möjligt för snabbare rendering eftersom webbläsaren utför det lägsta antalet reflows och repaints.

Det finns två sätt att associera ett DOM-element med en vy: ett nytt element kan skapas för vyn och därefter läggas till i DOM eller en referens kan göras till ett element som redan finns på sidan.

Om du vill skapa ett nytt element för din vy, ställ in en kombination av följande egenskaper i vyn: `tagName`, `id` och `className`. Ett nytt element kommer att skapas för dig av biblioteket och en referens till den kommer att finnas tillgänglig på `el`. Om inget tagnamn är angivet så blir det som standard en `div`.

I exemplet ovan, `tagName` är satt till "li", vilket resulterar i skapandet av ett li-element. Följande exempel skapar ett ul-element med id- och klassattribut:

```js
var TodoView = Backbone.View.extend({

    tagName: 'ul', // krävs, men standard blir annars en `div`.

    className: 'container', // valfritt, du kan tilldela flera klasser

    id: 'todos' // valfritt
})

var todoView = new TodoView()

console.log(todoView.el)
```

Ovanstående kod skapar DOM-elementet nedan men lägger inte till det i DOM.

```html
<ul id="todos" class="container"></ul>
```

Om elementet redan finns på sidan kan du ställa in `el` som en CSS-väljare som matchar elementet.

```js
el: '#footer'
```

Alternativt kan du ställa in `el` till ett befintligt element när du skapar vyn:

```js
var todosView = new TodosView({el: $('#footer')});
```

**Obs! När du deklarerar en vy, alternativen, `el`, `tagName`, `id` och `className` kan definieras som funktioner om du vill att deras värden ska bestämmas vid exekvering.**

#### eland()

Vy logik måste ofta påkalla jQuery eller Zepto funktioner på `el` element som är inbädda i vyn. 

Backbone gör det enkelt att göra det, genom att definiera `$el` egenskap och `$()` funktionen.

`view.$el` attribut motsvarar `$(view.el)` och `view.$(Selector)` motsvarar `$(view.el).find(selector)`.

I vårt TodoView-exempels renderingsmetod ser vi `this.$el` används för att ställa in elementets HTML och det `this.$()` används för att hitta dess subelement av klassen `edit`.

#### setElement

Om du behöver ange en existerande Backbone-vy till ett annat DOM-element kan `setElement` användas för detta ändamål.

Överstyrning this.el måste både ändra DOM-referensen och binda till händelserna till det nya elementet (och avbinda från det gamla). `setElement` skapar en cachad `$el` referens för dig, flyttar de delegerade händelserna för en vy från det gamla elementet till det nya.

```js
// Vi skapar två DOM-element som representerar knappar
var btn1 = $('<button></button>')
var btn2 = $('<button></button>')

// Definiera en ny vy
var TodoView = Backbone.View.extend({
    events: {
        click: function(e) {
            console.log(todoView.el === e.target)
        }
    }
})

// Skapa en ny instans av vyn, tillämpa den till btn1
var todoView = new TodoView({el: btn1})

// Applicera vyn på btn2 med setElement
todoView.setElement(btn2)

btn1.trigger('click')
btn2.trigger('click') // => true
```

Egenskapen "el" representerar markeringsdelen av den vy som ska skapas. För att få vyn att faktiskt rendera till sidan måste du lägga till den som ett nytt element eller lägga till ett befintligt element.

```js
// Vi kan också tillhandahålla raw markup till setElement
// som följer (bara för att visa att det kan göras):

var view = new Backbone.View;
view.setElement('<p><a><b>test</b></a></p>');
console.log(view.$('a b').html()); // => "test"
```

#### render()

render() är en valfri funktion som definierar logiken för att rendera en templeringsmall.

Vi använder Backbone mikro-templerande i dessa exempel, men kom ihåg att du kan använda andra templerande ramar om du föredrar det. Vårt exempel kommer att referera till följande HTML-markup:
