---
date: '2018-01-07 19:17 +0100'
published: true
title: JavaScritpt - Villkorlig (ternär) Operatör
---
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


