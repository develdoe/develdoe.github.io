---
date: '2017-04-10 13:46 +0200'
published: true
title: Node - Skapa och publicera en node.js modul
category:
  - Node
---
> Den här artikeln går igenom hur vi skapar och publiserar node moduler till NPM. Jag utgår ifrån att du har och använder node till vardags.


## Konfigurera NPM

Börja med att ange namn, email, och länk till din webbplats. När vi sedan köra NPM kommandon vet NPM vilka vi är och kommer att automatiskt fylla i den informationen åt oss.

```bash
npm set init.author.name 'Andree "DevelDoe" Ray'
npm set init.author.email 'me@andreeray.se'
npm set init.author.url "http://andreeray.se"
```
Nästa kommando kommer att be dig om e-post och lösenord, skapa/verifiera användaren i NPM registret och spara referenser till den i `~/.npmrc` filen.

```bash
npm adduser
```

## Din Modul

En NPM modul är bara vanlig JavaScript med en CommonJS syntaxen. 

Node modulerna körs i sitt eget uttrymme så att vi underviker konflikter i det globala namnuttrymmet. När vi sedan arbetar med modulerna, använder vi oss utav  `require` och `exports`.

```js
var other = require('other_module');
module.exports = function() {
    console.log(other.doSomething());
}
```

Som exempel, skapar vi ett API mot LocalStorage. NPM-modulen består av två metoder. En `set`, som tar en array, omvandlar den till en sträng och sedan lagrar strängen i `localStorage`. En `get` metod, som JSON parsar strängen tillbaka och returnerar den på anrop.

**Observera att om du kodar med, och planerar att publicera din modul till NPM, måste du ge din modul ett unikt namn.**

### Setup

Skapa/klona ett nytt repo på Github.

Skapa sedan en `package.json` fil genom att köra:

```bash
npm init
```

**package.json**

```json
{
  "name": "devel-localstorage",
  "version": "0.1.0",
  "description": "A small library providing an API for localStorage",
  "main": "index.js",
  "scripts": {
    "test": "karma start"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/AndreeDeveldoeRay/DevelLocalStorage.git"
  },
  "keywords": [
    "localstorage"
  ],
  "author": "Andree \"DevelDoe\" Ray <me@andreeray.se> (http://andreeray.se)",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/AndreeDeveldoeRay/DevelLocalStorage/issues"
  },
  "homepage": "https://github.com/AndreeDeveldoeRay/DevelLocalStorage#readme"
}
```

### Skapa

Vi börjar med vår primära modul.

**index.js**

```js
 module.exports = {
     /**
      * Takes an array and transforms it to a string for localStorage 
      * and stores is as 'store'
      * @param {Array} array
      */
     set: function (array) {
         if (Array.isArray(array)) {
             localStorage.setItem('store',JSON.stringify(array))
             return array
         }
     },
     /**
      * Gets the 'store in localStorage and parses it back to array (JSON)
      * @return {Array} returns the store as an array if it exist otherwise 
      * returns an empy array
      */
     get: function () {
         var stringArray = localStorage.getItem('store')
         var array = []
         try { array = JSON.parse(stringArray) } catch (e) {}
         return Array.isArray(array) ? array : []
     }
 }
```

**README.md**

```
<!--
@Author: Andreee Ray <develdoe>
@Date:   2017-04-10T17:45:02+02:00
@Email:  me@andreeray.se
@Filename: README.md
@Last modified by:   develdoe
@Last modified time: 2017-04-11T14:16:50+02:00
-->



=========

    A small library providing an API for localStorage

## Installation

    npm install devel-localstorage --save

## Usage

```js
    var localstorage = require('devel-localstorage')

    localstorage.set(['devel','doe'])
    res = localstorage.get()

    console.log('res:', res);
```

## Contributing

In lieu of a formal styleguide, take care to maintain the existing coding style.
Add unit tests for any new or changed functionality. Lint and test your code.

## Release History

* 1.0 Initial release
```


## Publicera och Uppdatera

Du updaterar ditt bibliotek genom att upprepa stegen nedan.

## Git commit 

```
git add .
git commit -m 'initial release'
git tag 0.1.0
git push --tags
```

## NPM 

Uppdatera versions nummer:
 
**package.json**

```json
"version": 0.1.0
```

Publicera:

```bash
npm publish
```

**Observara att du kan installera packet direkt ifrån Github**

```bash
npm install git://github.com/brentertz/scapegoat.git
npm install git://github.com/brentertz/scapegoat.git#0.1.0
```

## Install

```
npm install devel-localstorage
```

----

# Summering

Vid det här lager bör du se över hur du ska testa ditt packet. Jag uteslöt det här eftersom packetet använders sig av localStorage, som ju är en del av client-side JS. Test sviten blir såpass omfattande att jag har valt att göra en separat artikel för ändamålet. Länkar här så fort den är klar.