---
date: '2016-10-22 16:37 +0200'
published: true
title: React - Developmen Environment
socialImg: socialImgReact.png
category:
  - react
---
In this short article we are going to explore how to create an React environment with npm.

This improves resource management compared to the vary basic approach discussed in [Simple Code Example](http://develdoe.com/2016/react-simple-code-example/).


*Make dir*:  

```bash
$ mkdir reactEnv
```

```
$ cd reactEnv
```

*NPM modules*:

```bash
$ npm init
```

```bash
$ npm install -g webpack@1.12.13
```

```bash
$ npm install --save react@0.14.7 react-dom@0.14.7
```

```bash
$ npm install --save-dev webpack@1.12.13 babel-core@6.5.1 babel-loader@6.2.2 babel-preset-es2015@6.5.0 babel-preset-react@6.5.0
```



*Files*:

```
$ mkdir public public/components
```

```
$ touch server.js public/index.html public/app.js components/CLI.js
```

*/server.js*

```js
var express = require('express'),
    app     = express()


app.use(express.static('public'))

app.listen(4000, function(){
    console.log('Express server is up on port: 4000')
})
```

*public/index.html*  

```html
<!DOCTYPE html>
<html lang=en>
<head>
    <meta charset=utf-8>
</head>
<body>

    <div id=app></div>

    <script src=bundle.js></script>

</body>
</html>
```

*public/app.js*

```js
var CLI = require('./components/CLIs')

CLI()
```

*components/CLI.js*

```js
function CLI(){
    document.write('from init function')
}

module.exports = CLI
```

