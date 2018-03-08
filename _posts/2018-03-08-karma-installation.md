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