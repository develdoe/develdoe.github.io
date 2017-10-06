---
date: '2017-10-06 09:02 +0200'
published: true
title: Handlebars
---
Handlebars är en kraftfull mall (eng: templating) motor som är enkel att använda. Handlebars innehåller:

* Templates
* Expressions
* Context
* Helpers

Det är baserat på Mustasch mallens språk, men förbättrar den på flera viktiga sätt. Med Handlebars kan du skilja din HTML markup från din JavaScript och skriva renare kod.

Det rekommenderade sättet att lägga till mallar på din sida är att inkludera dem i `<script>` taggar med en speciell typ.Typattributet är viktigt, annars webbläsaren att försöka analysera dem som vanliga JavaScript's.

Mallarna har en lättfattlig syntax. De kan innehålla HTML och text, blandade med Handlebars uttryck.

Uttryckningar är inslagna i dubbel- eller trippelklamrar `{{}}`. Uttryck talar om för Handlebars att inkludera värden i variabler eller för att utföra funktioner i så kallade Helper functions.

---

## Templates

Mallar måste sammanställas till en JavaScript-funktion före användning. Du kan se ett exempel nedan:

**html**
```html
<script id="template" type="text/x-handlebars-template">Mitt namn är {% raw %} {{name}} {% endraw %}</script>

/*Det nya innehållet kommer att placeras här*/
<div class="content-placeholder"></div>
```

**JS**
```js
// hämta innehållet i vår mall
var templateScript = document.getElementById('template').innerHTML;

// Kompilera mallen
var template = Handlebars.compile(templateScript);

// Definera vårt data objekt
var context={
  "name": "Ray"
};

// Skicka data:n till vår mall
var theCompiledHtml = template(context);

// Skicka in vår compilerade html markup till dokumentet
document.getElementById('placeholder').innerHTML = theCompiledHtml
```

---

## Expressions

För att bygga upp från det föregående exemplet kommer all eventuella data som du skriver ut i ett {{}} uttryck, automatiskt att hämta behandlad HTML markup.

Det här är en bra säkerhetsfunktion, men ibland kanske du vill skriva ut rå HTML. I det här fallet kommer du att använda trippelklamers uttryck {{{}}}.

Observera också att Handlebars uttryck stödjer nästlade värden som gör att vi enkelt kan komma åt data från vilket  JavaScript objekt som helst.

**HTML**
```html
<script id="template" type="text/x-handlebars-template">
{% raw %}
    {{description.escaped}}
    {{example}}
    <br><br>
    {{description.unescaped}}
    {{{example}}}
{% endraw %}
</script>

<!--Your new content will be displayed in here-->
<div id="placeholder"></div>
```

**JS**
```js
// hämta innehållet i vår mall
var templateScript = document.getElementById('template').innerHTML;

// Kompilera mallen
var template = Handlebars.compile(templateScript);

// Define our data object
var context={
    "description": {
      "escaped": "Using {{}} brackets will result in escaped HTML:",
      "unescaped": "Using {{{}}} will leave the context as it is:"
    },
    "example": "<button> Hello </button>"
  };

// Pass our data to the template
var theCompiledHtml = template(context);

// Add the compiled html to the page
document.getElementById('placeholder').innerHTML = theCompiledHtml
```

---

## Context

Handlebars kontext är ett js objekt där vi lägger till våra egna. 

Alla Mallar har ett kontext. På översta nivån är det JavaScript-objektet som du skickar till den sammanställda mallen.

Men hjälpare som #each eller #with modifierar det, så att du direkt kan komma dess egenskaper för att iterera objektet.

**HTML**
```html
<!-- The #each helper itererar över array matriser. -->
<script id="template" type="text/x-handlebars-template">

  <!-- people is looked up on the global context, the one we pass to the compiled template -->

  {% raw %}{{#each people}}{% endraw %}

    <!-- Here the context is each individual person. So we can access its properties directly: -->
    <p>{% raw %}{{firstName}} {{lastName}}{% endraw %}</p>

  {{/each}}

</script>
```
**JS**
```js
// hämta innehållet i vår mall
var templateScript = document.getElementById('template').innerHTML;

// Kompilera mallen
var template = Handlebars.compile(templateScript);

// This is the default context, which is passed to the template
var context = {
people: [ 
  { firstName: 'Homer', lastName: 'Simpson' },
  { firstName: 'Peter', lastName: 'Griffin' },
  { firstName: 'Eric', lastName: 'Cartman' },
  { firstName: 'Kenny', lastName: 'McCormick' },
  { firstName: 'Bart', lastName: 'Simpson' }
]
};

// Pass our data to the template
var theCompiledHtml = template(context);

// Add the compiled html to the page
document.body.innerHTML = theCompiledHtml;
```

---

## Helpers

Handlebars tillåter dig inte att skriva JavaScript direkt i mallar, istället ger det dig hjälpare.

Det här är JavaScript-funktioner som du kan anropa från dina mallar, och hjälper dig att återanvända kod och skapa komplexa mallar.

To call a helper, just use it as an expression `{% raw %}{{helpername}}{% endraw %}`.

Du kan också vidarebefordra parametrar `{% raw %}{{help name 12345}}{% endraw %}`, som skickas som parametrar till din hjälparfunktion.

För att skapa en hjälpare, använder du registerHelper funktionen. 

**HTML**
```html
<script id="template" type="text/x-handlebars-template">
{% raw %}{{#each animals}} {% endraw %}
	<p>
    	The {% raw %} {{capitalize this.name}} {% endraw %} says 
        {% raw %} {{#if this.noize}} {% endraw %}
        {% raw %} {{this.noize}} {% endraw %}
        {% raw %} {{#else}} {% endraw %}
        nothing at all
        {% raw %} {{#/if}} {% endraw %}
{% raw %} {{#/each}} {% endraw %}
</script>

<div id="placeholder"></div>
```

**JS**
```js
// Registrera en hjälpare
Handlebars.registerHelper('capitalize', function(str){
	// str är argumentet till hjälparen när anropad
  	str = str || '';
    return str.slice(0,1).toUpperCase() + str.slice(1);
});

// hämta innehållet i vår mall
var templateScript = document.getElementById('template').innerHTML;

// Kompilera mallen
var template = Handlebars.compile(templateScript);

// Vår kontext
var context = {
    animals:[
      {
        name: "cow",
        noise: "moooo"
      },
      {
        name: "cat",
        noise: "meow"
      },
      {
        name: "fish",
        noise: ""
      },
      {
        name: "farmer",
        noise: "Get off my property!"
      }
    ]
};

// Pass our data to the template
var theCompiledHtml = template(context);

// Add the compiled html to the page
document.body.innerHTML = theCompiledHtml;
```

---

## Block helpers

Blockhjälpare är precis som de vanliga, men de har en öppning och en avslutande tagg (som **#if** och **#each** inbyggda).

Dessa hjälpare kan ändra HTML innehållet i de kontext de omfångar. De är lite mer komplicerade att skapa, men är mycket kraftfulla. Du kan använda dem för att återanvända funktionalitet eller skapa stora block av HTML på ett återanvändbart sätt. Som exempelvist en lista av objekt som du återanvänder på flera ställen i din applikation.

För att skapa en blockhjälpare använder du igen `Handlebars.registerHelper()`.