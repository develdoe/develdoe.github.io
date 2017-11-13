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

Vi använder Handlebars i dessa exempel, men kom ihåg att du kan använda andra templerande ramverk om du föredrar det. Vårt exempel kommer att referera till följande HTML-markup:

```html
<!DOCTYPE HTML>
<html>
<head>
    <meta charset="UTF-8">
    <title>Backbone Boilerplate</title>
</head>
<body>

<div id="todo"></div>

<script id="template" type="text/x-handlebars-template">
    <div>
        <input id="todo_complete" type="checkbox" {{#if competed}} checked="checked" {{/if}}>
        {{title}}
    </div>
</script>

<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js"></script>
<script src="http://underscorejs.org/underscore-min.js"></script>
<script src="http://backbonejs.org/backbone-min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/4.0.11/handlebars.min.js"></script>
<script src=todoView.js></script>
```

Metoden Handlebars.compile i Handlebars sammanställer JavaScript-mallar till funktioner som kan utvärderas för rendering. 

I TodoView passerar jag markup från mallen med id `template` till Handlebars.compile för att sammanställas och lagras i egenskapen `todoTpl` när vyn skapas.

Metoden `render()` använder denna mall genom att skicka den till `toJSON()` för att överföra till attributen för modellen som är associerad med visningen.

Mallen returnerar sin markering efter att ha använt modellens `title` och `completed` flagga för att utvärdera uttrycken som innehåller dem. Jag ställer sedan in denna markering som HTML-innehållet i `el` DOM-elementet med egenskapen `$el`.

Detta fyller i mallen, vilket ger dig en data-komplett uppsättning markup på bara några korta rader av kod.

En vanlig Backbone-konvention är att returnera `this` slutet av render(). Detta är användbart av ett antal skäl, inklusive:

* Göra vyer lätt återanvändbara i andra föräldravisningar.
* Skapa en lista med element utan att rendera och måla var och en av dem individuellt, bara för att målas upp när hela listan är populerad.

Låt oss försöka implementera den senare av dessa. render metoden för en enkel ListView som inte använder en ItemView för varje objekt kan skrivas:

```js
var ListView = Backbone.View.extend({

    // Kompilera en mall för den här vyn. I detta fall '...'
    // är en platshållare för en mall som $("#list_template").html()
    template: Handlebars.compile(...),

    render: function() {
        this.$el.html(this.template(this.model.attributes))
        return this
    }
})
```

Låt oss nu anta att det har fattats beslut att konstruera objekten med hjälp av en ItemView för att ge förbättrat beteende till vår lista. ItemView kan skrivas:

```js
var ItemView = Backbone.View.extend({
    events: {},
    render: function() {
        this.$el.html( this.template(this.model.attributes))
        return this
    }
})
```

Notera användningen av returnera `this` i slutet av render. Detta vanliga mönster gör det möjligt för oss att återanvända vyn som en undervy. Vi kan också använda den för att för-rendera vyn innan rendering. Med hjälp av detta krävs att vi ändrar vår ListViews renderingsmetod enligt följande:

```js
var ListView = Backbone.View.extend({
    render: function() {

        // Antag att vår modell visar de saker vi kommer att göra
        // visa i vår lista
        var items = this.model.get('items')

        // Loop genom var och en av våra artiklar med Underscore
        // _.each iterator
        _.each(items, function(item){

            // Skapa en ny instans av ItemView, passerar
            // det ett specifikt modellobjekt
            var itemView = new ItemView({ model: item });

            // Objektets DOM-element läggs till efter det
            // att det har renderats. Här är 'return this' hjälpsamt
            // när itemView gör sin modell. Senare ber vi om
            // dess output ("el")
            this.$el.append( itemView.render().el );
        }, this);
    }
})
```

#### The events hash

Backbones händelser hash tillåter oss att bifoga händelse lyssnare till antingen el-relativa anpassade selektorer, eller direkt till el om ingen väljare tillhandahålls. 

En händelse har formen av ett nyckelvärdespar `eventName selector': 'callbackFunction` och ett antal DOM-händelse-typer stöds, inklusive `click, submit, mouseover, dblclick`, mm.

```js
var TodoView = Backbone.View.extend({

    tagName: 'li',

    // med en händelsehash som innehåller DOM-händelser
    events: {
        'click .toggle': 'toggleCompleted',
        'dblclick label': 'edit',
        'keypress .edit': 'updateOnEnter',
        'click .destroy': 'clear',
        'blur '
    }
})
```

Det som inte är direkt uppenbart är att medan Backbone använder jQuery's `.delegate()` underytan, tar Backbone det ett steg vidare genom att utvidga det så att `this` alltid refererar till det aktuella vyns objekt inom callback funktioner.

Det enda som verkligen är att tänka på är att någon sträng callbackback som levereras till händelseattributet måste ha en motsvarande funktion med samma namn inom ramen för din vy.

De deklarativa delegerade jQuery-händelserna betyder att du inte behöver oroa dig för huruvida ett visst element har renderats till DOM ännu eller ej. Vanligtvis med jQuery måste du oroa dig för "närvaro eller frånvaro i DOM" hela tiden med bindande händelser.


Observera att du även kan binda metoder själv med hjälp av `_.bind(this.viewEvent, this)`, vilket är vad värdet i varje händels nyckelparametrar gör. Nedan använder vi `_.bind` för att återrendera vår vy när en modell ändras.

```js
var TodoView = Backbone.View.extend({
    initialize: function() {
        this.model.bind('change',_.bind(this.render,this))
    }
})
```

_.bind fungerar bara på en metod i taget, men binder en funktion till ett objekt så att när som helst som funktionen kallas kommer värdet av `this` att vara objektet. _.bind stöder också att skicka in argument till funktionen för att fylla in dem i förväg - en teknik som kallas [partial application](http://benalman.com/news/2012/09/partial-application-in-javascript/).

## Kollektioner

Kollektioner är uppsättningar av Modeller och skapas genom att förlänga Backbone.Collection.

Normalt, när du skapar en kollektion, vill du också definiera en egenskap som anger typen av modell som din kollektion innehåller, tillsammans med eventuella instans attribut som krävs.

I följande exempel skapar vi en TodoCollection som innehåller våra Todo-modeller:

```js
var Todo = Backbone.Model.extend({
    defaults: {
        title: '',
        completed: false
    }
})

var TodoCollection = Backbone.Collection.extend({
    model: Todo
})

var myTodo = new Todo({title:'title',id:2})

// Passera en rad modeller på kollektion instansiering
var todos = new TodoCollection([myTodo])
console.log("Collection size: " + todos.length) // => Collection size: 1
```

### Adding and Removing Models

Det föregående exemplet populerade kollektioner med hjälp av en rad modeller när den var instansierad. Efter att en kollektion har skapats kan modeller läggas till och tas bort med metoderna `add()` och `remove()`:

```js
var Todo = Backbone.Model.extend({
  defaults: {
    title: '',
    completed: false
  }
})

var TodosCollection = Backbone.Collection.extend({
  model: Todo
})

var a = new Todo({title:'a'}),
    b = new Todo({title:'b'}),
    c = new Todo({title:'c'})

var todos = new TodosCollection([a,b])

todos.add(c)

todos.remove([a,b])

todos.remove(c)
```

Observera att `add()` och `remove()` accepterar både enskilda modeller och listor av modeller.

Observera också att när du använder `add()` på en kollektion, leder {{merge: true} till duplicerade modeller att få deras attribut sammanfogas till befintliga modeller istället för att ignoreras.

```js
var items = new Backbone.Collection;
items.add([{id:1, name: 'dog', age: 3}, {id:2, name: 'cat', age: 2}])
items.add([{id:1, name: 'bear', age: 3}], {merge:true})
items.add([{id:2, name: 'Andree'}])  // ignoreras 
```

### Retrieving Models

Det finns några olika sätt att hämta en modell från en kollektion. Den mest raka framåt är att använda `Collection.get()` som accepterar ett enda ID enligt följande:

```js
var Todo = Backbone.Model.extend({
    defaults: {
        title: '',
        completed: false
    }
})

var TodosCollection = Backbone.Collection.extend({
  model: Todo
})

var myTodo = new Todo({title:'my title', id: 2})

var todos = new TodosCollection([myTodo])

var todo2 = todo.get(2)
```

I klient-server applikationer innehåller kollekitioner modeller som erhållits från servern.

När du utbyter data mellan klienten och en server behöver du ett sätt att unikt identifiera modeller. I Backbone görs detta med `id`, `cid` och `idAttribute` attribut.

Varje modell i Backbone har ett id, vilket är en unik identifierare som är antingen ett heltal eller en sträng (t.ex. en UUID).

Modeller har också ett `cid` (klient id) som automatiskt genereras av Backbone när modellen skapas. Endera identifieraren kan användas för att hämta en modell från en kollektion.

Huvudskillnaden mellan dem är att `cid` genereras av Backbone; Det är till hjälp när du inte har ett riktigt id. Detta kan vara fallet om din modell ännu inte har sparats på servern eller om du inte sparar den till en databas.

`IdAttribute` är identifieringsattributnamnet för modellen som returneras från servern (dvs id i din databas). Detta berättar för Backbone vilket datafält från servern ska användas för att fylla i id-egenskapen (tänk på det som en mapper).

Som standard är det `id`, men det kan anpassas efter behov. Om din server, till exempel, anger ett unikt attribut på din modell med namnet "userId", skulle du ange `idAttribute` till "userId" i din modelldefinition.

Värdet på en modells idAttribute bör ställas in av servern när modellen sparas. Efter denna punkt behöver du inte ställa in den manuellt, om inte ytterligare kontroll krävs.

Internt innehåller `Backbone.Collection` en uppsättning modeller som räknas upp av deras id-egendom, om modell instanser råkar ha en. När collection.get(id) anropas, kontrolleras denna array för existens av modell instans med motsvarande ID.

```js
var Todo = Backbone.Model.extend({
    defaults: {
        title: '',
        completed: false
    }
})

var TodosCollection = Backbone.Collection.extend({
  model: Todo
})

var myTodo = new Todo({title:'my title', id: 2})

var todos = new TodosCollection([myTodo])

var todo2 = todos.get(2)

var todoCid = todos.get(todo2.cid)
```

### Listening for events

Eftersom kollektioner representerar en grupp av objekt kan vi lyssna på att lägga till och ta bort händelser som uppstår när modeller läggs till eller tas bort från en kollektion. Här är ett exempel:

```js
var Todo = Backbone.Model.extend({
    defaults: {
        title: 'mr Ray',
        competed: true
    }
})

var todo = new Todo({
    title: 'fuck'
})

var TodosCollection = new Backbone.Collection()



TodosCollection.on('add', function(todo) {
    console.log("I should " + todo.get('title') + ". Have I done it before? " + (todo.get('competed') ? 'yeah!' : 'no'))
})

TodosCollection.add(todo)
```

Dessutom kan vi binde till en `change` händelse för att lyssna på ändringar i någon av modellerna i kollektionen.

```js
var Todo = Backbone.Model.extend({
    defaults: {
        title: 'mr Ray',
        competed: true
    }
})

var fuck = new Todo({ title: 'fuck' }),
    suck = new Todo({ title: 'suck', competed: false}),
    lick = new Todo({ title: 'lick'})

var TodosCollection = new Backbone.Collection()

TodosCollection.on('change:title', function(todo) {
    console.log("I should " + todo.get('title') + ". Have I done it before? " + (todo.get('competed') ? 'yeah!' : 'no'))
})

TodosCollection.add([fuck,suck,lick])

fuck.set('title','beeep')
```

jQuery-stil händelsekartor av formuläret `obj.on({click: action})` kan också användas. Dessa kan vara tydligare än att behöva tre separata anrop till `.on` och borde anpassa sig bättre med händelserna som används i vyer:

```js
var Todo = Backbone.Model.extend({
    defaults: {
        title: 'mr Ray',
        completed: true
    }
})

var myTodo = new Todo()
myTodo.set({title: 'Buy some cookies', completed: true})

myTodo.on({
    'change:title': titleChanged,
    'change:completed': completedChanged
})

function titleChanged() {
    console.log('the title was changed')
}

function completedChanged() {
    console.log('completed state changed')
}

myTodo.set({title: 'Get some groceries'})
```

Backbone händelser stöder också en `once()` metod, vilket säkerställer att en callback bara exekveras en gång när en notifikatino kommer. Det liknar Node's `once`, eller jQuery's `one`. Detta är särskilt användbart för när du vill säga "nästa gång något händer, gör det här".

```js
// Definiera ett objekt med två räknare
var TodoCounter = {counterA: 0, counterB: 0}

// Increment counterA, utlöser en händelse
var incrA = function() {
    TodoCounter.counterA += 1
    //Denna utlösning ger ingen effekt på räknarna
    TodoCounter.trigger('event')
}

// Increment counterB
var incrB = function() {
    TodoCounter.counterB += 1
}

// Använd once snarare än att uttryckligen koppla bort vår händelse lyssnare
TodoCounter.once('event', incrA)
TodoCounter.once('event', incrB)

// Utlös händelsen för första gången
TodoCounter.trigger('event')

console.log(TodoCounter.counterA === 1); // true
console.log(TodoCounter.counterB === 1); // true
```

counterA och counterB bör bara ha ökats en gång.

### Återställ/Refreashing Kollektioner

I stället för att lägga till eller ta bort modeller enskilt, kanske du vill uppdatera en hel samling på en gång.

`Collection.set()` tar en rad modeller och utför nödvändig `add`, `remove` och `change` operationer som krävs för att uppdatera samlingen.

```js
var TodoCollection = new Backbone.Collection()

TodoCollection.add([
    { id: 1, title: 'go to Jamaica', completed: false},
    { id: 2, title: 'go to China', completed: false},
    { id: 3, title: 'go to Disneyland', completed: true}
])

// Vi kan lyssna på add/change/remove events
TodoCollection.on('add', function(module) {
    console.log("add: " + model.get('title'))
})

TodoCollection.on('remove', function(module) {
    console.log("remove: " + model.get('title'))
})

TodoCollection.on('change', function(module) {
    console.log("chagne: " + model.get('title'))
})

TodosCollection.set([
    { id: 1, title: 'go to Jamaica.', completed: true },
    { id: 2, title: 'go to China.', completed: false },
    { id: 4, title: 'go to Disney World.', completed: false }
])

// Ovan logs:
// Completed go to Jamaica.
// Removed go to Disneyland.
// Added go to Disney World.
```

Om du bara behöver ersätta hela innehållet i samlingen kan `Collection.reset()` användas:

```js
var TodosCollection = new Backbone.Collection()

// we can listen for reset events
TodosCollection.on("reset", function() {
console.log("Collection reset.");
})

TodosCollection.add([
  { title: 'go to Jamaica.', completed: false },
  { title: 'go to China.', completed: false },
  { title: 'go to Disneyland.', completed: true }
])

console.log('Collection size: ' + TodosCollection.length); // Collection size: 3

TodosCollection.reset([
  { title: 'go to Cuba.', completed: false }
]);
// Above logs 'Collection reset.'

console.log('Collection size: ' + TodosCollection.length); // Collection size: 1
```

Ett annat användbart tips är att använda reset utan argument för att rensa ut en kollektion helt. Det här är praktiskt när du laddar upp en ny sida med resultat dynamiskt där du vill blanka ut den aktuella resultaten.

```js
myCollection.reset()
```

Observera att Collection.reset() inte avfyrar händelserna add, remove. En reset händelse avfyras istället som visas i föregående exempel. Anledningen till att du kanske vill använda detta är att utföra superoptimerad rendering i extrema fall där enskilda händelser är för dyra.

Observera också att du lyssnar på en återställningshändelse. Listan med tidigare modeller är tillgänglig i `options.previousModels`, för enkelhetens skull.

```js
var todo = new Backbone.Model()
var todos = new Backbone.Collection([todo]).on('reset', function(todos, options) {
        console.log(options.previousModels)
        console.log([todo])
        console.log(options.previousModels[0] === todo)
    })

todos.reset([]);
```

Den `set()` metoden som är tillgänglig för kollektioner kan också användas för "smarta" uppdatering av listor av modeller.

Den här metoden försöker utföra smart uppdatering av en kollektioner med en viss lista över modeller. När en modell i denna lista inte finns i samlingen läggs den till. Om det är närvarande, kommer dess attribut att slås samman. Modeller som finns i kollektionen men inte i listan tas bort.

```js
// Definiera en modell av typen "Beatle" med ett "job" attribut
var Beatle = Backbone.Model.extend({
    defaults: {
        job: 'musician'
    }
})

// Skapa modeller för varje medlem av Beatles
var john = new Beatle({ firstName: 'John', lastName: 'Lennon'});
var paul = new Beatle({ firstName: 'Paul', lastName: 'McCartney'});
var george = new Beatle({ firstName: 'George', lastName: 'Harrison'});
var ringo = new Beatle({ firstName: 'Ringo', lastName: 'Starr'});

// Skapa en samling med våra modeller
var theBeatles = new Backbone.Collection([john, paul, george, ringo]);

// Skapa en separat modell för Pete Best
var pete = new Beatle({ firstName: 'Pete', lastName: 'Best'});

// Uppdatera samlingen
theBeatles.set([john, paul, george, pete]);
```

### Underscore utility funktioner

Backbone utnyttjar fullt ut sitt hårda beroende av Underscore genom att göra många av sina verktyg direkt tillgängliga på kollektioner:

`forEach`: iterera över kollektioner
```js
var todos = new Backbone.Collection();

todos.add([
  { title: 'go to Belgium.', completed: false },
  { title: 'go to China.', completed: false },
  { title: 'go to Austria.', completed: true }
]);

todos.forEach(function(model){
  console.log(model.get('title'));
});

// Ovan logs:
// go to Belgium.
// go to China.
// go to Austria.
```

`sortBy()`: sortera en kollektion på ett specifikt attribut:
```js
// sort collection
var sortedByAlphabet = todos.sortBy(function (todo) {
    return todo.get("title").toLowerCase();
});

console.log("- Now sorted: ");

sortedByAlphabet.forEach(function(model){
  console.log(model.get('title'));
});

// Ovan logs:
// - Now sorted:
// go to Austria.
// go to Belgium.
// go to China.
```

`map()`: iterera genom en kollektion, mappning av varje värde genom en transformationsfunktion
```js
var count = 1;
console.log(todos.map(function(model){
  return count++ + ". " + model.get('title');
}));
// Above logs:
//1. go to Belgium.
//2. go to China.
//3. go to Austria.
```

`min()/max()`: hämta objekt med värdet min eller max för ett attribut
```js
todos.max(function(model){
  return model.id;
}).id;

todos.min(function(model){
  return model.id;
}).id;
```

`pluk()`: extrahera ett specifikt attribut
```js
var captions = todos.pluck('caption');
// returns list of captions
```

`ilter()` filtrera en kollektion
```js
var Todos = Backbone.Collection.extend({
  model: Todo,
  filterById: function(ids){
    return this.filter(
      function(c) { 
        return _.contains(ids, c.id); 
      })
  }
});
```

`idexOf()` returnera index för ett visst objekt i en kollektion
```js
var people = new Backbone.Collection;

people.comparator = function(a, b) {
  return a.get('name') < b.get('name') ? -1 : 1;
};

var tom = new Backbone.Model({name: 'Tom'});
var rob = new Backbone.Model({name: 'Rob'});
var tim = new Backbone.Model({name: 'Tim'});

people.add(tom);
people.add(rob);
people.add(tim);

console.log(people.indexOf(rob) === 0); // true
console.log(people.indexOf(tim) === 1); // true
console.log(people.indexOf(tom) === 2); // true
```

`any()`: bekräfta om något av värdena i en kollektion passerar ett iterator sanningstest
```js
todos.any(function(model){
  return model.id === 100;
});

// or
todos.some(function(model){
  return model.id === 100;
});
```

size(): returnera storleken på en samling
```js
todos.size();

// equivalent to
todos.length;
```

`isEmpty()`: bestäm om en kollektion är tom
```js
var isEmpty = todos.isEmpty();
```

`groupBy()`: gruppera en kollektion i grupper av liknande föremål
```js
var todos = new Backbone.Collection();

todos.add([
  { title: 'go to Belgium.', completed: false },
  { title: 'go to China.', completed: false },
  { title: 'go to Austria.', completed: true }
]);

// skapa grupper av färdiga och ofullständiga modeller
var byCompleted = todos.groupBy('completed');
var completed = new Backbone.Collection(byCompleted[true]);
console.log(completed.pluck('title'));
// logs: ["go to Austria."]
```

Dessutom finns flera av Underscore-operationerna på objekt tillgängliga som metoder på Modeller.

`pick()`: extrahera en uppsättning attribut från en modell
```js
var Todo = Backbone.Model.extend({
  defaults: {
    title: '',
    completed: false
  }
});

var todo = new Todo({title: 'go to Austria.'});
console.log(todo.pick('title'));
// logs {title: "go to Austria"}
```

`omit()`: extrahera alla attribut från en modell utom de som anges
```js
var todo = new Todo({title: 'go to Austria.'});
console.log(todo.omit('title'));
// logs {completed: false}
```

`keys()` och `values()` få listor över attributnamn och värden
```js
var todo = new Todo({title: 'go to Austria.'});
console.log(todo.keys());
// logs: ["title", "completed"]

console.log(todo.values());
//logs: ["go to Austria.", false]
```

`pairs()`: få lista över attribut som `[key, value]` par
```js
var todo = new Todo({title: 'go to Austria.'});
var pairs = todo.pairs();

console.log(pairs[0]);
// logs: ["title", "go to Austria."]
console.log(pairs[1]);
// logs: ["completed", false]
```

`invert()`: skapa objekt där `values` är `keys` och attributen är värden
```js
var todo = new Todo({title: 'go to Austria.'});
console.log(todo.invert());

// logs: {'go to Austria.': 'title', 'false': 'completed'}
```

Den fullständiga listan över vad Underscore kan göra finns i dess officiella [dokumentation](http://underscorejs.org/).

### Kedjbart API

När man talar om verktygsmetoder är en annan bit av socker i Backbone sitt stöd för Underscore's chain() metoden.

Kedja är ett vanligt idiom i objektorienterade språk; en kedja är en sekvens av metodanrop på samma objekt som utförs i ett enda uttalande.

Medan Backbone gör Underscores array manipulationsoperationer tillgängliga som metoder för kollektionsobjekt kan de inte direkt kopplas eftersom de returnerar arrays snarare än den ursprungliga samlingen.

Lyckligtvis kan införandet av Underscore's `chain()` metoden du kedja anrop till dessa metoder på kollektioner.

`chain()` metoden returnerar ett objekt som har alla Underscore array-operationer kopplade som metoder vilket returnerar det objektet. Kedjan slutar med ett anrop till value() metoden som helt enkelt returnerar det resulterande matrisvärdet. Om du inte har sett det tidigare ser det här kedjbara API:

```js
var collection = new Backbone.Collection([
  { name: 'Tim', age: 5 },
  { name: 'Ida', age: 26 },
  { name: 'Rob', age: 55 }
]);

var filteredNames = collection.chain() // startkedja, returnerar en wrapper runt kollektionens modeller
    .filter(function(item) { return item.get('age') > 10; }) // returnerar förpackad array exklusive Tim
    .map(function(item) { return item.get('name'); }) // returnerar förpackad array som innehåller de namn som är kvar
    .value() // Avslutar kedjan och returnerar den resulterade arrayen.

console.log(filteredNames); // logs: ['Ida', 'Rob']
```

Några av de Backbone specifika metoderna returnerar `this`, vilket innebär att de även kan vara kedjiga:

```js
var collection = new Backbone.Collection();

collection
    .add({ name: 'John', age: 23 })
    .add({ name: 'Harry', age: 33 })
    .add({ name: 'Steve', age: 41 });

var names = collection.pluck('name');
console.log(names); // logs: ['John', 'Harry', 'Steve']
```

## RESTful Persistence

Hittills har alla våra exempeldata skapats i webbläsaren. För de flesta SPA applikationer är modellerna härledda från en datalager som finns på en server. Det här är ett område där Backbone dramatiskt förenklar koden du behöver skriva för att utföra RESTful synkronisering med en server genom ett enkelt API på dess modeller och samlingar.

### Hämtar modeller från servern

`Collections.fetch()` hämtar en uppsättning modeller från servern i form av en JSON-array genom att skicka en HTTP GET-förfrågan till URL-adressen som anges av samlingens `url` egenskap (vilket kan vara en funktion). När denna data tas emot kommer en `set()` att utföras för att uppdatera kollektionen.

```js
var Todo = Backbone.Model.extend({
  defaults: {
    title: '',
    completed: false
  }
});

var TodosCollection = Backbone.Collection.extend({
  model: Todo,
  url: '/todos'
});

var todos = new TodosCollection();
todos.fetch(); // sends HTTP GET to /todos
```

### Spara modeller till servern

Medan Backbone kan hämta en hel samling modeller från servern samtidigt, uppdateras modellerna individuellt med modellens `save()` metod. När `save()` kallas på en modell som hämtats från servern, den konstruerar en URL genom att lägga till modellens id till kollektionens URL och skickar en HTTP-PUT till servern.

Om modellen är en ny instans som skapades i webbläsaren (dvs det har inget ID) skickas en HTTP POST till kollektionens URL.

`Collections.create()` kan användas för att skapa en ny modell, lägga till den i kollektionen och skicka den till servern i ett enda metodanrop.

```js
var Todo = Backbone.Model.extend({
  defaults: {
    title: '',
    completed: false
  }
});

var TodosCollection = Backbone.Collection.extend({
  model: Todo,
  url: 'http://35.189.253.140/api/todos'
});


var todos = new TodosCollection();
todos.fetch(); // sends HTTP GET to /todos

var todo2 = todos.get(2);
todo2.set('title', 'go fishing');
todo2.save(); // sends HTTP PUT to /todos/2

todos.create({title: 'Try out code samples'}); // sends HTTP POST to /todos and adds to collection
```

Som nämnts tidigare kallas en modells `validate()` metod automatiskt genom att `save()` och kommer att utlösa en ogiltig händelse på modellen om validering misslyckas.

### Radera modeller från servern

En modell kan tas bort från den innehållande kollektionen och servern genom att anropa dess `destroy()` metod.

Till skillnad från `Collection.remove()` som bara tar bort en modell från en kollektion, skickar `Model.destroy()` också en HTTP DELETE till kollektionens URL.

```js
var Todo = Backbone.Model.extend({
  defaults: {
    title: '',
    completed: false
  }
});

var TodosCollection = Backbone.Collection.extend({
  model: Todo,
  url: '/todos'
});

var todos = new TodosCollection();
todos.fetch();

var todo2 = todos.get(2);
todo2.destroy(); // sends HTTP DELETE to /todos/2 and removes from collection
```

Åberopa `destory` på en Modell kommer att returnera falskt om modellen är `isNew`:

```js
var todo = new Backbone.Model();
console.log(todo.destroy());
// false
```

### Options

Varje RESTful API-metod accepterar en mängd olika alternativ. Viktigast av allt accepterar alla metoder success- och error callbacks som kan användas för att anpassa hanteringen av serverns svar.

Att ange `{patch: true}` alternativet till `Model.save()` kommer att få det att använda HTTP PATCH för att bara skicka de ändrade attributen (dvs partiella uppdateringar) till servern istället för hela modellen. dvs `model.save(attrs, {patch: true})`:

```js
// Save partial using PATCH
model.clear().set({id: 1, a: 1, b: 2, c: 3, d: 4});
model.save();
model.save({b: 2, d: 4}, {patch: true});
console.log(this.syncArgs.method);
// 'patch'
```

På samma sätt kommer överföring av `{reset: true}` till `Collection.fetch()` att resultera i att kollektionen uppdateras med `reset()` istället för `set()`.

Se dokumentationen Backbone.js för fullständiga beskrivningar av de stödda alternativen.

## Events

Händelser är en grundläggande inversion av kontroll. Istället för att ha ett funktionsanrop som är ett annat med namn, registreras den andra funktionen som en hanterare som ska anropas när en viss händelse inträffar.

Den del av din applikation som måste veta hur du anropar till den andra delen av din app har blivit inverterad. Detta är kärnpunkten som gör det möjligt för din business logic att inte behöva veta hur ditt användargränssnitt fungerar och är det mest kraftfulla med Backbone Events-systemet.

Mastering-händelser är ett av de snabbaste sätten att bli mer produktiva med Backbone, så låt oss ta en närmare titt på Backbones eventmodell.

`Backbone.Events` är mixade in de andra Backbone "klasserna", inklusive:
* Backbone
* Backbone.Model
* Backbone.Collection
* Backbone.Router
* Backbone.History
* Backbone.View







