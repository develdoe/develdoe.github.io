---
date: '2016-11-02 20:57 +0100'
published: true
title: JavaScript - map
socialImg: js.png
category:
  - js
---
*map är ett mycket användbar koncept att bemästra, så det är väl värt att ta tid att förstå det. Förhoppningsvis kommer denna korta guide hjälper dig att förstå det.*

map tar en matris och en funktion, applicerar sedan funktionen på varje element i matrisen och returnerar en ny matris som innehåller resultatet.

```javascript
var numbers = [1, 4, 9]; // > [1, 4, 9]
var roots = numbers.map(Math.sqrt); //  > [1, 2, 3]
```

Funktionen tas som argument av map kallas för en callback, eftersom som den utförs på varje element i den ursprungliga arrayen. 

Du måste skriva argument funktionen på ett mycket specifikt sätt, den måste:

* Ta in det aktuella elementet som skall behandlas som argument.
* Returnera ett nytt element som kommer att ta sin plats i den nya matrisen.

*Eventuellt kan funktionen också ta i en ett sekundärt index argument som motsvarar platsen i arrayen) hos det aktuella elementet, och en tredje matrisargument som pekar tillbaka till den ursprungliga arrayen.*

```javascript
incrementByOne = function (element) {
  return element + 1;
}

myArray = [1,2,3,4];

myArray.map(incrementByOne); // > [2,3,4,5]
```

*Notera att en foreach, tillskillnad från map, inte returnerar något. Det anropar en funktion en gång per element, men inte göra något med resultaten av dessa samtal.

Hittills har vi definierat vår callback's separat, en funktion för varje map. Men vi kan ju använda en praktisk förkortning i form av anonyma funktioner.


```javascript
myArray = [1,2,3,4];

myArray.map(function (element) {
  return element + 1;
});
```

Nyligen införde JavaScript en ny, kortare syntax för att definiera anonyma funktioner. Känd som "fat arraw" ES2015 (eller ES6) syntaxen.

Det låter dig ersätta den traditionella funktion (argument) {...} syntax med mycket kortare:

```javascript
myArray = [1,2,3,4];

myArray.map(element => {
  return element + 1;
});
```

Eller ännu kortare (om du endast returnerar ett värde:

```javascript
myArray.map(element => element + 1);
```

React använder en syntax som kallas JSX. Du kan i princip tänka på det som HTML med små bitar av JavaScript.


Till skillnad från mall språk som Handlebars eller HAML, så kommer inte JSX med en inbyggd each hjälpare. I stället när du vill slinga över en array du använder map.

Vi mappar myList matrisen till en funktion som tar i ett element, och returnerar en bit av JSX som sveper den i en <li> tag:

```javascript
<ul>
  {myList.map(function (element) {
    return (
      <li>{element}</li>
    )
  })}
</ul>
``` 

Eller:

```javascript
<ul>
  {myList.map(element => <li>{element}</li>)}
</ul>
```