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
	// Standard todo attributvärden
	defaults: {
    	title: 'Todo Title',
      	completed: false
    }
})

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








