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

En NPM modul är bara vanlig JavaScript, men med en CommonJS syntax. 

Node modulerna körs i sitt eget uttrymme så att vi underviker konflikter i det globala namnuttrymmet. När vi sedan arbetar med modulerna, använder vi oss utav  `require` och `exports`.

```js
var other = require('other_module');
module.exports = function() {
    console.log(other.doSomething());
}
```

Som exempel, skapar jag ett API mot LocalStorage. NPM-modulen består av två metoder. En `set`, som tar en array, omvandlar den till en sträng och en `get` metod, som JSON parsar strängen tillbaka.

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
  "version": "1.0.0",
  "description": "A small library providing an API for localStorage",
  "main": "index.js",
  "scripts": {
    "test": "make test"
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
  "homepage": "https://github.com/AndreeDeveldoeRay/DevelLocalStorage#readme",
  "devDependencies": {
    "chai": "^3.5.0",
    "mocha": "^3.2.0"
  }
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

För att undervika att Node modulerna sparas i ditt repo lägger vi till undantaget i en `.gitignore` fil.

**.gittignore**

```
node_modules
```

## Test 

### Installera

Installera Mocha och Chai:

```bash
npm install karma karma-mocha expect karma-chrome-launcher --save-dev
```

### Konfigurera 

**Makefile***

```
test:
    ./node_modules/.bin/mocha --reporter spec

.PHONY: test
```

**Observera att det måste vara tab och inte blanksteg under `test:`**

**package.json**

```json
...
"scripts": {
  "test": "make test"
},
...
```

### Test

**/test/index.js**

```js
var should      	= require('chai').should(),
    localstorage   	= require('..index'),
    escape      = escapeXSS.escape,
    unescape    = escapeXSS.unescape

describe('# set', function () {
    it('should convert array into string and set it in localStorage', function () {
        escape('&amp;').should.equal('&')
    })
})
describe('# get', function() {
    it('should convert array into string and return it ;', function () {
        escape('&').should.equal('&amp;')
    })
})
```

### Kör 

```
npm test
```

## README

För att formatera README filen kan man använda sig av markdown, exempelvist: 

```md
=========

A small library providing utility methods to `escape` and `unescape` HTML entities

## Installation

  npm install scapegoat --save

## Usage

  var scapegoat = require('scapegoat')
      escape = scapegoat.escape,
      unescape = scapegoat.unescape;

  var html = '<h1>Hello World</h1>',
      escaped = escape(html),
      unescaped = unescape(escaped);

  console.log('html', html, 'escaped', escaped, 'unescaped', unescaped);

## Tests

  npm test

## Contributing

In lieu of a formal styleguide, take care to maintain the existing coding style.
Add unit tests for any new or changed functionality. Lint and test your code.

## Release History

* 1.0 Initial release
```

uppdatera version:

```json
"version": 1.0
```

Tagga: 

```
git tag 1.0
git push origin master --tags
```

## Publicera

```bash
npm publish
```

**Observara att du kan installera packet direkt ifrån Github**

```bash
npm install git://github.com/brentertz/scapegoat.git
npm install git://github.com/brentertz/scapegoat.git#0.1.0
```

## Update

När du gör ändringar kan du uppdatera paket med NPM version `<update_type>`, där update_type är en av de semantiska typerna versionshanterin, patch, minor, or major:

```
npm version <update_type>
npm publish
```

## Install

```
npm install scapegoat
```

## Verifiera

För att testa din nya modul kan du skapa ett test projekt:

```
mkdir testDevelLocalStorage 
cd testDevelLocalStorage
npm install devel-localstorage
```

**index.js**

```js
 var develescape = require('devel-escape-xss')
       escape = develescape.escape,
       unescape = develescape.unescape;

   var html = '<h1>Hello World</h1>',
       escaped = escape(html),
       unescaped = unescape(escaped);

   console.log('html', html, 'escaped', escaped, 'unescaped', unescaped);
```

```
node index.js
```