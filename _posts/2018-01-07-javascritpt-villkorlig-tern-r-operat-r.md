---
date: '2018-01-07 19:17 +0100'
published: true
title: JavaScritpt - Villkorlig (ternär) Operatör
---
*Denna artikel är en översättning och bearbetad version av original artikeln [Conditional (ternary) Operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator) på MDN.*

Den villkorliga (ternära) operatören är den enda JavaScript-operatören som tar tre operander. Denna operatör används ofta som en genväg istället för en if-sats.

## Syntax

```js
condition ? expr1 : expr2 
```

### Parametrar

`conditions (eller flera vilkor)` Är ett uttryck som utvärderar till sant eller falskt.

`xpr1, expr2` Är uttryck med värden av vilken typ som helst.

## Beskrivning

Om `condition` är sant returnerar operatören värdet av `expr1`; annars `expr2`.

Om du till exempel vill visa ett annat meddelande baserat på värdet på isMember-variabeln kan du använda detta uttryck:

```js
'The fee is ' + (isMember ? '$2.00' : '$10.00')
```

Du kan också tilldela variabler beroende på ett ternärt resultat:

```js
var elvisLives = Math.PI > 4 ? 'Yep' : 'Nope' // => 'Yep'
```

Du kan också använda flera villkor som i ett flera villkor IF-uttryck:

```js
var condition1 = true,
    condition2 = false,
    access = condition1 ? (condition2 ? "true true": "true false") : (condition2 ? "false true" : "false false")

console.log(access); // logs "true false"
```

*Obs! Parametrarna är inte nödvändiga och påverkar inte funktionaliteten. De är där för att hjälpa till att visualisera hur utfallet behandlas.*

Du kan också använda ternära utvärderingar i ledigt utrymme för att göra olika operationer:

```js
var stop = false, age = 16

age > 18 ? location.assign('continue.html') : stop = true
```

Du kan också göra mer än en enskild operation per fall, skilja dem med ett kommatecken och bifoga dem i parentes:

```JS
var stop = false, age = 23;

age > 18 ? (
    alert('OK, you can go.'),
    location.assign('continue.html')
) : (
    stop = true,
    alert('Sorry, you are much too young!')
)
```

Du kan också göra mer än en operation under tilldelningen av ett värde. I detta fall, **the last comma-separated value of the parenthesis will be the value to be assigned.**

```js
var age = 16;

var url = age > 18 ? (
    alert('OK, you can go.'), 
    // alert returns "undefined", but it will be ignored because
    // isn't the last comma-separated value of the parenthesis
    'continue.html' // the value to be assigned if age > 18
) : (
    alert('You are much too young!'),
    alert('Sorry :-('),
    // etc. etc.
    'stop.html' // the value to be assigned if !(age > 18)
)

location.assign(url); // "stop.html"
```

## Returnera ternära uttryck

Den ternära operatören lånar sig bra för att ge avkastning som annars skulle kräva ett `if/else` uttryck.

```js
var func1 = function( .. ) {
  if (condition1) { return value1 }
  else { return value2 }
}

var func2 = function( .. ) {
  return condition1 ? value1 : value2
}
```

Ett bra sätt att upptäcka att den ternära är en lämplig ersättning för en `if/else` uttryck är när `return` nyckelord används flera gånger och är det enda uttrycket inuti if-block.

Genom att bryta ternaryen på flera linjer och utnyttja extra blankutrymme kan den ternära operatören användas för att mycket rent ersätta en lång rad `if/else` uttryck.

```js
var func1 = function( .. ) {
  if (condition1) { return value1 }
  else if (condition2) { return value2 }
  else if (condition3) { return value3 }
  else { return value4 }
}

var func2 = function( .. ) {
  return condition1 ? value1
       : condition2 ? value2
       : condition3 ? value3
       :              value4
}
```
