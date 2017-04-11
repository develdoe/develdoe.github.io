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

## Create a node module

En Node NPM module är bara vanilla JS skript, men med CommonJS syntax. 

Node modulerna körs i sitt eget uttrymme så att vi underviker namnkonflikter i det globala namnuttrymmet. När vi sedan arbetar med modulerna, använder vi oss utav  `require` och `exports`.

```js
var other = require('other_module');
module.exports = function() {
    console.log(other.doSomething());
}
```

Som exempel, skapar vi ett API mot LocalStorage. NPM-modul består av två metoder. En `set`,  tar en array, och skapar en sträng som vi lagrar, Och en `get` metod, som JSON parsar strängen tillbaka till JSON format.

**Observera att om du kodar med, och planerar att publicera din modul till NPM, måste du ge din modul ett unikt namn, duh... =)**

Skapa/klona ett nytt repo på Github och en `package.json`:

```bash
npm init
```

```json
{
  "name": "devel-localstorage",
  "version": "0.0.1",
  "description": "an API for local storage",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/AndreeDeveldoeRay/npm-DevelLocalStorage.git"
  },
  "keywords": [
    "localstorage"
  ],
  "author": "Andree \"DevelDoe\" Ray <me@andreeray.se> (http://andreeray.se)",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/AndreeDeveldoeRay/npm-DevelLocalStorage/issues"
  },
  "homepage": "https://github.com/AndreeDeveldoeRay/npm-DevelLocalStorage#readme"
}
```

*Ytterligare detaljer och förklaring av innehållet i package.json filen kan hittas på [npm.org/docs/json.html](https://npmjs.org/doc/json.html)*

Börja med att skapa filen `index.js`, som innehåller vår primära module.

**index.js**

```js
module.exports = {
    /**
     * Escape special characters in the given string of html.
     * @type {Object}
     * @param {String} html
     * @return {String}
     */
    escape: function (html) {
        return String(html)
            .replace(/&/g, '&amp;')
            .replace(/"/g, '&quot;')
            .replace(/'/g, '&#39;')
            .replace(/</g, '&lt;')
            .replace(/>/g, '&gt;')
    },
    /**
     * Unescape special characters in the given string of html.
     * @type {Object}
     * @param {String} html
     * @return {String}
     */
    unescape: function (html) {
        return String(html)
            .replace(/&amp;/g, '&')
            .replace(/&quot;/g, '"')
            .replace(/&#39;/g, "'")
            .replace(/&lt;/g, '<')
            .replace(/&gt;/g, '>')
    }
}
```

Installations kommandon ovan skapar också Node moduler i ditt projekt. För att undervika att Node modulerna sparas i ditt repo lägger vi till undantaget i en `.gitignore` fil.

**.gittignore**

```
node_modules
```

## Test 

### Installera

Installera Mocha och Chai:

```bash
npm install mocha --save-dev
npm install chai --save-dev
```

### Skapa

**/test/index.js**

```js
var should      = require('chai').should(),
    escapeXSS   = require('..index'),
    escape      = escapeXSS.escape,
    unescape    = escapeXSS.unescape

describe('# escape', function() {
    it('should convert & into &amp;', function () {
        escape('&').should.equal('&amp;')
    })
    it('should convert " into &quot;', function () {
        escape('"').should.equal('&quot;')
    })
    it("should convert ' into &#39;", function () {
        escape("'").should.equal('&#39;')
    })
    it('should convert < into &lt;', function () {
        escape('<').should.equal('&lt;')
    })
    it('should convert > into &gt;', function () {
        escape('>').should.equal('&gt;')
    })
})

describe('# unescape', function () {
    it('should convert &amp; into &', function () {
        escape('&amp;').should.equal('&')
    })
    it('should convert &quot; into "', function () {
        escape('&quot;').should.equal('"')
    })
    it("should convert &#39; into '", function () {
        escape('&#39;').should.equal("'")
    })
    it('should convert &lt; into <', function () {
        escape('&lt;').should.equal('<')
    })
    it('should convert &gt; into >', function () {
        escape('&gt;').should.equal('>')
    })
})
```

### Konfigurera 

**Makefile***

```
test:
    ./node_modules/.bin/mocha --reporter spec

 .PHONY: test
```

**package.json**

```json
...
"scripts": {
  "test": "make test"
},
...
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