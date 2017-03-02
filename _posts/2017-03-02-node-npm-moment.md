---
date: '2017-03-02 14:25 +0100'
published: true
title: Node - NPM moment
category:
  - Node
---
[moment](http://momentjs.com/)

```
npm install moment --save-dev
```

```js
var moment = require('moment')

console.log(moment().format())

var now = moment()

console.log('current timestamp', now.unix())

var timestamp = 1488458642
var currentMoment = moment.unix(timestamp)
console.log('currentMoment',currentMoment.format('MMM D, Y @ H:mm'))
```
