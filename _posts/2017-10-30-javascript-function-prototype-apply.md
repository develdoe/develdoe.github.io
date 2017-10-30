---
date: '2017-10-30 14:57 +0100'
published: true
title: JavaScript - Function.prototype.apply()
---
*Denna artikel är en svensk översättning av [Function.prototype.apply()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply) på MDN.*

Metoden `apply()` kallar en funktion med ett givet värde och argument som tillhandahålls som en array (eller ett array-liknande objekt).

**Obs! Medan syntaxen för denna funktion är nästan identisk med den för call(), är den grundläggande skillnaden att call() accepterar en argumentlista, medan apply() accepterar en enda grupp av argument.**

### Syntax

```js
func.apply(thisArg, [argsArray])
```

### Parametrar

`thisArg` - Värdet av this anges som anrop till func. Observera att detta kanske inte är det verkliga värdet som ses av metoden. Om metoden är en funktion i none-strict läget, kommer null och odefinierad att ersättas med det globala objektet och primitiva värden kommer att boxas.

`argsArray` - Ett array-liknande objekt, med angivande av argumenten som func ska kallas, eller null eller undefined om inga argument ska tillhandahållas till funktionen. Från och med ECMAScript 5 kan dessa argument vara ett generiskt array-liknande objekt istället för en array. Se nedan för information om webbläsarkompatibilitet.

### Retur värde

Resultatet av att anropa funktionen med det angivna this värde och argument.

## Beskrivning