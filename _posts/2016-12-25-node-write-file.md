---
date: '2016-12-25 13:59 +0100'
published: true
title: Node - Write File
category:
  - Node
---
```js
var fs = require('fs');
fs.writeFile("/tmp/test", "Hey there!", function(err) {
    if(err) {
        return console.log(err);
    }

    console.log("The file was saved!");
}); 
```