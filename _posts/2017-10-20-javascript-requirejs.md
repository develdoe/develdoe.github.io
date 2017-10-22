---
date: '2017-10-20 15:39 +0200'
published: true
title: JavaScript - RequireJS
---
Att ladda in dina JavaScript-filer via flera `<script>` taggar är mycket enkelt, men att göra det på så sätt har många nackdelar, inklusive ökning av HTTP-överhead.

Varje gång webbläsaren laddar in en fil som du har refererat till i en `<script>` tagg, gör den en HTTP-begäran om att ladda in filens innehåll. Den gör en ny sådan HTTP-begäran för varje fil du vill ladda in, vilket orsakar problem:

* Webbläsare är begränsade i hur många parallella förfrågningar de kan göra, så ofta är det långsamt att ladda flera filer, eftersom det bara kan göra ett visst antal i taget. Detta nummer beror på användarens inställningar och webbläsare, men är vanligtvis runt 4-8. När du arbetar med webb-applikationer är det bra att dela upp din app i flera JS-filer, så det är enkelt att snabbt nå den gränsen. Detta kan negeras genom att du compilerar din kod i en fil som en del av en byggprocess, men hjälper inte när skript laddas synkront. Det innebär att webbläsaren inte kan fortsätta måla sidan medan skriptet laddas.

Verktyg som RequireJS gör så att script laddas asynkront. Detta betyder att du måste anpassa din kodbas något. Du kan inte bara byta ut `<script>` element för en liten bit av RequireJS-kod, men fördelarna är värdefulla:

* Laddar skriptna asynkront betyder att lastprocessen är icke-blockerande. Webbläsaren kan fortsätta måla upp resten av sidan när skriptna laddas, vilket gör att den första laddningstiden påskyndas.
* Vi kan ladda modulerna mer intelligent, ha mer kontroll över när de laddas och se till att moduler som har beroenden laddas i rätt ordning.

## Behov av bättre beroendehantering

Dependenshantering är ett utmanande ämne, i synnerhet när du skriver JavaScript i webbläsaren.

Som standard, gällande beredskapshantering, är helt enkelt att se till att vi anropar våra scripts med `<script>` taggar så att kod som beror på kod i en annan fil laddas efter den fil det beror på. Detta är inte ett bra tillvägagångssätt.

Tänk på Gmail webbklienten för ett ögonblick. När en användare initialt laddar sidan vid sitt första besök, kan Google helt enkelt dölja widgets som chattmodulen tills användaren har angett (genom att klicka på "expandera") att de vill använda den. Genom att ladda ner dynamiskt beroende kan Google ladda upp chattmodulen vid den tiden, i stället för att tvinga alla användare att ladda den när sidan initialiseras.

## Asynkronmodulsdefinition (AMD)

RequireJS implementerar [AMD-specifikationen](https://github.com/amdjs/amdjs-api/wiki/AMD) som definierar en metod för att skriva modulär kod och hantera beroenden. RequireJS webbplatsen har också en [sektion som dokumenterar orsakerna till att implementera AMD](http://requirejs.org/docs/whyamd.html).

> AMD-formatet kommer från att ha ett modulformat som var bättre än dagens "skriva ett gäng skriptetiketter med implicita beroenden som du måste beställa manuellt" och något som var lätt att använda direkt i webbläsaren. Något med bra felsökningsegenskaper som inte krävde serverspecifik verktyg för att komma igång.

## Skriva AMD-moduler med RequireJS

Såsom diskuterats ovan är det övergripande målet för AMD-formatet att tillhandahålla en lösning för modulärt JavaScript som utvecklare kan använda idag.

Det finns två koncept som du måste vara medveten om när du använder de med en skriptlasstare. De två koncepten är `define()` metoden, för att definiera moduler samt metoden `require()` för att ladda beroenden. 

`define()` används för att definiera namngivna eller ej namngivna moduler med följande signatur:

```js
define(
    module_id /*optional*/,
    [dependencies] /*optional*/,
    definition function /*function for instantiating the module or object*/
);
```

module_id ett valfritt argument som vanligtvis endast krävs när icke AMD konkatenation används (det kan finnas några andra specialfall där det också är användbart). När detta argument uteslutas kallas modulen "anonym". När du arbetar med anonyma moduler, kommer RequireJS att använda modulens filväg som dess module_id, Don't Repeat Yourself (DRY) bör tillämpas genom att utelämna module_id i `define()` anrop.

`[dependencies]` argumentet är en array som representerar alla andra moduler som denna modul beror på.

Det tredje argumentet är en funktion för att skapa en instans av en modul eller ett objekt.

En barebones-modul (kompatibel med RequireJS) kan definieras med hjälp av `define()` enligt följande:

```js
// Ej anononym definition
define('myModule', 
    ['math', 'graph'], 
    function ( math, graph ) {
 
        // Note that this is a slightly different pattern
        // With AMD, it's possible to define modules in a few
        // different ways due as it's relatively flexible with
        // certain aspects of the syntax
        return {
            plot: function(x, y){
                return graph.drawPie(math.randomGrid(x,y));
            }
        }
    };
});
```

*Obs! RequireJS är intelligent nog för att automatiskt lägga till ".js" -tillägget till dina skriptfilnamn. Som sådan utelämnas denna förlängning i allmänhet när man specificerar beroenden.*

#### Alternativ syntax

Det finns också en alternativ version av `define()` som låter dig att deklarera dina beroenden som lokala variabler med `require()`. Detta kommer att känna sig bekant för alla som har använt Node och kan vara enklare att lägga till eller ta bort beroenden:

```js
// note that I also made it anonymous. 
define(function (require ) {
  
        var foo = require('math');
        var bar = require('graph');
 
        // Note that this is a slightly different pattern
        // With AMD, it's possible to define modules in a few
        // different ways due as it's relatively flexible with
        // certain aspects of the syntax
        return {
            plot: function(x, y){
                return graph.drawPie(math.randomGrid(x,y));
            }
        }
    };
});
```

Metoden require() används vanligtvis för att ladda kod högst upp i en JavaScript-fil eller inom en modul, när du dynamiskt vill hämta beroenden. Ett exempel på dess användning är:

```js
// Consider 'foo' and 'bar' are two external modules
// In this example, the 'exports' from the two modules loaded are passed as
// function arguments to the callback (foo and bar)
// so that they can similarly be accessed

require( ['foo', 'bar'], function ( foo, bar ) {
    // rest of your code here
    foo.doSomething();
});
```

Addys post på [Writing Modular JS](https://addyosmani.com/writing-modular-js/) täcker AMD-specifikationen i mycket mer detalj. Definiera och använda moduler kommer snart att behandlas när vi tittar på mer strukturerade exempel på att använda RequireJS.

## Komma igång med RequireJS

