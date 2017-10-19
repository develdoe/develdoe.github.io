---
date: '2017-10-18 11:59 +0200'
published: true
title: JavaScript - Arguments object
---
`arguments` objektet är ett Array-liknande objekt som motsvarar de argument som överförs till en funktion.

Du kan använda `arguments` objektet om du aropar en funktion med fler argument än det formellt förklaras att acceptera.Denna teknik är användbar för funktioner som kan överföras ett variabelt antal argument.

Använd `arguments.length` för att bestämma antalet argumenter som skickats till funktionen, och bearbeta sedan varje argument genom att använda `arguments` objektet.

För att bestämma antalet parametrar i funktionens [signatur](https://developer.mozilla.org/en-US/docs/Glossary/Signature/Function), använd egenskapen `Function.length` metoden.

* `arguments` objektet är en lokal variabel tillgänglig inom alla (non-arrow) funktioner.
* Du kan referera till en funktions arguments inom funktionen med hjälp av `arguments` objektet. 
* Detta objekt innehåller en post för varje argument som skickas till funktionen, index börjar vid 0.

Om en funktion exempelvis har skickat tre argument kan du hänvisa till dem enligt följande:

```js
arguments[0]
arguments[1]
arguments[2]
```

Argument kan också ställas in:

```js
arguments[1] = 'new value';
```

`arguments` objektet är inte en `Array`. `arguments` liknar en `Array`, men har inga av dess egenskaper förutom `length`. Det har till exempel inte `pop` metoden. Men det kan konverteras till en riktig Array:

```js
var args = Array.prototype.slice.call(arguments);
var args = [].slice.call(arguments);

// ES2015
const args = Array.from(arguments);
```` 

**Använda `slice`på `arguments` förhindrar optimeringar i vissa JavaScript-motorer (V8 till exempel). Försök istället att bygga en ny array genom att iterera genom argumentobjektet istället. Ett alternativ skulle vara att använda den föragtiga `Array` konstruktören som en funktion:**

```js
var args = (arguments.length === 1 ? [arguments[0]] : Array.apply(null, arguments));
```

## Använda `typeof` med Arguments

`typeof` av `arguments` returnerar ett objek".

```js
console.log(typeof arguments); // 'object'
console.log(typeof arguments[0]); // typeof individual arguments.
```

## Använda Spread Syntax med Arguments

Du kan också använda metoden `Array.from()` eller spridningsoperatören för att konvertera argument till en riktig Array:

```js
var args = Array.from(arguments);
var args = [...arguments];
```

## Egenskaper

[arguments.callee](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments/caller) - Hänvisning till den nuvarande exekveringsfunktionen
[arguments.length](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments/length) - Hänvisning till antalet argument som skickats till funktionen
[argument[@@ iterator]](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments/@@iterator)  Returnerar ett nytt Array Iterator-objekt som innehåller värdena för varje index i argumenten
