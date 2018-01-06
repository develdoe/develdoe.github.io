---
date: '2018-01-06 12:57 +0100'
published: true
title: JavaScript - Logiska operatörer
---
Logiska operatörer används vanligtvis med booleska (logiska) värden. När de är, returnerar de ett booleskt värde.

`&&` och `||` operatörerna returnerar det faktisktska värdet av en av de angivna operanderna, så om dessa operatörer används med icke-booleska värden kan de returnera ett icke-booleskt värde.

```js
var flag = true
console.log( flag || false ) // => true

console.log( flag && false) // => false
```

Logical NOT `!` Returnerar falskt om dess operand kan konverteras till true; annars returnerar sant.

```js
var flag = true
console.log( ! flag || false ) // => false
```

När man blandar dessa operatörer med aritmetik och andra operatörer är det inte allt självklart hur det tolkas. Operatörsförrang bestämmer hur operatörer analyseras med avseende på varandra. Operatörer med högre prioritet blir operandörerna hos operatörer med lägre prioritet. 