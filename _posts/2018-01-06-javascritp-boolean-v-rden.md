---
date: '2018-01-06 10:07 +0100'
published: true
title: JavaScritp - Boolean Värden & Komperation
---
Ofta behöver man värden som är antingen sanna eller falska. I programmeringsvärlden använder vi en speciel typ som heter "Boolean", dessa kan bara evalueras till `true` eller `false`.

# Komparation

`>` och `<` är symboler för "mer än" och "mindre än". Dessa är binära operatorer som resulterar till en sträng som indikerar om värdet är sant eller falskt

```js
console.log(3 > 2)      // => true
console.log(3 < 2)      // => false

console.log("a" < "b")  // => true
console.log("a" > "b")  // => false
```

Strängar är mer eller mindra sorterad alfabetiskt. Stora bokstäver är alltid mindre än små, så  `"Z" < "a"` är sant. Icke alfabetiska tecken (!.,,- mm) är också inkluderade i sortering.

Sorteringen är baserad på *Unicode* standarden. Denna standard tilldelar ett nummer till virtuellt alla tecken du kommer någonsin använda. Att ha sådana nummer är praktiskt att lagra i en dator eftersom det gör det möjligt att representera dem som en sekvens nummer. 

När man jämför strängar, görs det från vänster till höger, på dess numeriska värden, en och en. 

Andra likanande operatorer är `>=` (störren än eller lika med), `<=` (mindre än eller lika med), `==` (lika med), != (inte lika med).

```js
console.log("rayman" != "ratman") // => true
```

Det finns bara ett värde som inte är lika med sig själv och det är `NaN` (not a number).
`NaN` representerar ett ickeSansat uträkning ooch är därför inte lika resultatet av ytterligare ett `NaN`:

```js
console.log(NaN == NaN) // => false
```

