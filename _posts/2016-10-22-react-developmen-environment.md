---
date: '2016-10-22 16:37 +0200'
published: true
title: React - Developmen Environment
socialImg: socialImgReact.png
category:
  - react
---
In this short article we are going to set up an environment for developing with React.js

*Make dir*:  

```bash
$ mkdir reactEnv
$ cd reactEnv
```

*NPM modules*:

```bash
$ npm init
$ npm install react react-dom --save
$ npm install babel webpack webpack-dev-server -g
$ npm install babel-loader babel-core babel-preset-es2015 babel-preset-react
```

*Files*:

```
$ touch index.html App.js main.js webpack.config.js
```

*/index.html*  

```html
<head>
    <meta charset=utf-8>
    <title>Setup</title>
</head>
<body>
    <div id=app></div>
    <script src=index.js></script>
</body>
```

*/webpack.config.js*  

```javascript
module.exports = {
    entry: './main.js',
    output: {
        path: './',
        filename: 'index.js'
    },
    devServer: {
        inline: true,
        port: 3333
    },
    module: {
        loaders:[
            {
                test: /\.js$/,
                exclude: /node_modules/,
                loader: 'babel',
                query:{
                    presets: ['es2015','react']
                }
            }
        ]
    }
}
```

*/App.js*  

```javascript
import React from 'react';
class App extends React.Component{
    render(){
        return <div>Helllo</div>
    }
}
export default App
```

*/main.js*  

```javascript
import React from 'react'
import ReactDOM from 'react-dom'
import App from './App.js'
ReactDOM.render(<App />,document.getElementById('app'))
```

*/package.json*  

```json
{
  "name": "reactenv",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "webpack-dev-server"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "react": "^15.3.2",
    "react-dom": "^15.3.2"
  }
}
```

