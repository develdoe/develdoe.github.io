---
date: '2016-12-07 16:33 +0100'
published: true
title: React - Boilerplate
category:
  - react
---
In this article we are going to explore how to setup a React environment with Webpack and Reload.

This improves resource management compared to the vary basic approach discussed in [Simple Code Example](http://develdoe.com/2016/react-simple-code-example/).


**Make**  

```bash
$ mkdir react-boilerplate
```

```
$ cd react-boilerplate
```

**Modules**

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
$ npm install --save-dev webpack@1.12.13 babel-core@6.5.1 babel-loader@6.2.2 babel-preset-es2015@6.5.0 babel-preset-react@6.5.0 babel-preset-stage-0
```



**Files**

```
$ mkdir public app app/components
```

```
$ touch public/index.html app/entry.jsx webpack.config.js server.js
```

**./server.js**

```js
var express = require('express'),
    app     = express()


app.use(express.static('public'))

app.listen(4000, function(){
    console.log('Express server is up on port: 4000')
})
```

**./webpack.config.js**

```js
module.exports = {
    entry: './app/entry.jsx',
    output: {
        path: __dirname,
        filename: './public/bundle.js'
    },
    resolve: {
        root: __dirname,
        alias: {
        },
        extensions: ['','.js','.jsx']
    },
    module: {
        loaders: [
            {
                loader: 'babel-loader',
                query: {
                    presets: ['react','es2015','stage-0']
                },
                test: /\.jsx?$/,
                exclude: /(node_modules|bower_components)/
            }
        ]
    }
}
```

**./public/index.html** 

```html
<!DOCTYPE html>
<html>
<head> <meta charset=utf-8> </head>

<body>

    <div id=app></div>

    <script src=bundle.js></script>


```

*./app/entry.jsx*

```js
var React       = require('react'),
    ReactDOM    = require('react-dom')

ReactDOM.render(
    <h1>React - Boilerpate</h1>,
        document.getElementById('app')
)

```

Run: 

```bash
webpack -w
```

Upen new terminal window:

```bash
reload -b
```
