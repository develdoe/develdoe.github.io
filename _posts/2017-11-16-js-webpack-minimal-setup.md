---
date: '2017-11-16 17:33 +0100'
published: true
title: JS - Webpack minimal setup
---
```js
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  }
}
```
