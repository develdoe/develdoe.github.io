---
date: '2017-10-18 11:59 +0200'
published: true
title: JavaScript - Arguments object
---
`arguments` objektet är ett Array-liknande objekt som motsvarar de argument som överförs till en funktion.

## Description

`arguments` objektet är en lokal variabel tillgänglig inom alla (non-arrow) funktioner.

Du kan referera till en funktions argument inom funktionen med hjälp av `arguments` objektet. 

Detta objekt innehåller en post för varje argument som skickas till funktionen, den första postens index börjar vid 0.

Om en funktion exempelvis har skickat tre argument kan du hänvisa till dem enligt följande:

```js
arguments[0]
arguments[1]
arguments[2]
```


