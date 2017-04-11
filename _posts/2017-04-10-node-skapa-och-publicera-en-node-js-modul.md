---
date: '2017-04-10 13:46 +0200'
published: true
title: Node - Skapa och publicera en node.js modul
category:
  - Node
---
> Att skapa en node module ohc publicera den till NPM är en ganska okomplicerad process. Här är en snabb handledning för att snabbt få dig på fötter.

## Vad är NPM?

NPM är ett online-register för öppen källkod för node.js projekt, moduler, resurser, mm. Du hittar NPM på [npmjs.org](http://npmjs.org)

NPM är också den officiella pakethanteraren för node.js och ger ett kommandoradsgränssnitt (CLI) för att interagera med registret. Detta verktyg levereras med node.js och installeras automatiskt. API-dokumentationen hittar du på [npmjs.org/doc/](http://npmjs.org/doc), alternativt kan du helt enkelt köra kommandot `npm` i terminal.

## Installera

Du måste så klart installera node.js och NPM för att skapa moduler. Det finns lite olika sätt att installera npm, tre alternativen:

* Homebrew - `brew install node`
* Binärt - [nodejs.org](http://nodejs.org)
* NVM (Node Version Manager) [nvm](https://github.com/creationix/nvm) (rekommenderat) 

## Konfigurera

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

En Node NPM module är bara vanilla JS skript, men med CommonJS syntax. Lyckligtvist är detta verkligen inte komplicerat.

Node moduler körs i sitt eget uttrymme, för att undervika konflikt i det globala namnuttrymmet. Node tillhandahåller vissa "globals" för att hjälpa till med modul interoperabilitet. De 2 primära sakar vi arbetar med är "require" och "exports". Man använder `require` för att ladda in script som du behöver för ditt script och exporterar ditt script med `exports`.

```js
var other = require('other_module');
module.exports = function() {
    console.log(other.doSomething());
}
```

För vår demo, skapar vi en NPM-modul bestårende av ett par metoder för "escaping" och "unescaping" HTML-enheter - något vi ofta behöver göra för att förhindra XSS-attacker vid rendering användargenererat innehåll.

**Observera att om du kodar med, och planerar att publicera din modul till NPM, måste du ge din modul ett unikt namn.**

För att komma igång, skapade jag ett nytt repo på mitt Github konto, samt initerade ett nytt git projekt för det repot. 

Skapa sedan en `package.json` fil genom att köra:

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
/**
 * @Author: Andreee Ray <develdoe>
 * @Date:   2017-04-10T17:51:20+02:00
 * @Email:  me@andreeray.se
 * @Filename: index.js
 * @Last modified by:   develdoe
 * @Last modified time: 2017-04-10T18:00:47+02:00
 *
 * @desc: escaping and unescaping HTML entities – commonly needed utility to prevent XSS attacks when rendering user generated content.
 */


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
            .replace(/>/g, '&gt')
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
            .replace(/&quot;/g, '""')
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

Installera Mocha och Chai:

```bash
npm install mocha --save-dev
npm install chai --save-dev
```

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

När vi sedan ska köra våra tester, kan vi följa Mochas dokumentation och skapa filen Makefile och associera vårt test:

**Makefile***

```
test:
    ./node_modules/.bin/mocha --reporter spec

 .PHONY: test
```

Och köra kommandot:

```
make test
```

Men vi kan förbättra processen och istället konfigurera NPM att köra våra tester:

**package.json**

```json
...
"scripts": {
  "test": "make test"
},
...
```

Och sen köra: 

```
npm test
```
