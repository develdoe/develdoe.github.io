---
date: '2018-03-08 14:11 +0100'
published: true
title: Karma - Installation
---
Karma körs på Node.js och finns som ett [NPM-paket](https://www.npmjs.com/package/karma).

Det rekommenderade tillvägagångssättet är att installera Karma (och alla plugins som ditt projekt behöver) lokalt i projektets katalog.

```bash
# Install Karma:
$ npm install karma --save-dev

# Install plugins that your project needs:
$ npm install karma-jasmine karma-chrome-launcher jasmine-core --save-dev
```

Detta kommer att installera `karma`, `karma-jasmine`, `karma-chrome-launcher` och `jasmine-core` packet till `node_modules` i din nuvarande arbetsmapp och spara dem som `devDependencies` i `package.json`, så att alla andra utvecklare som arbetar med projektet bara behöver göra `npm install` för att få alla dessa beroenden installerade.

```bash
# Run Karma:
$ ./node_modules/karma/bin/karma start
```

Att skriva `./node_modules/karma/bin/karma start` suger så kanske du tycker att det är användbart att installera `karma-cli` globalt. Du måste göra detta om du vill köra Karma på Windows från kommandoraden.

```bash
$ npm install -g karma-cli
```

Då kan du köra Karma från var som helst med `karma` och den kommer alltid att köra den lokala versionen.
