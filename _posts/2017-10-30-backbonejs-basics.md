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

Ett exempel på detta (av @fivetanley) finns [här](http://jsfiddle.net/2NdDY/270/).

Observera också att validering vid initialisering är möjlig men med begränsad användning, eftersom objektet som konstrueras är internt märkt ogiltigt men ändå vidarebefordrat till den som åberoparen (fortsätter ovanstående exempel):




