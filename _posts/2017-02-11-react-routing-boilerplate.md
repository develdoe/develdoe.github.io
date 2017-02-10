---
date: '2017-02-11 00:01 +0100'
published: true
title: React - Routing Boilerplate
category:
  - React
---
**/public/index.html**

```html
<!DOCTYPE html>
<html>
<head>

    <meta charset=utf-8>

    <link rel=stylesheet media=all href=setup.css>

</head>

<body>

    <div id=app></div>

    <script src=bundle.js></script>
```

**/public/setup.css**

```css
html, body, div, span, applet, object, iframe,
h1, h2, h3, h4, h5, h6, p, blockquote, pre,
a, abbr, acronym, address, big, cite, code,
del, dfn, em, img, ins, kbd, q, s, samp,
small, strike, strong, sub, sup, tt, var,
b, u, i, center,
dl, dt, dd, ol, ul, li,
fieldset, form, label, legend,
table, caption, tbody, tfoot, thead, tr, th, td,
article, aside, canvas, details, embed,
figure, figcaption, footer, header, hgroup,
menu, nav, output, ruby, section, summary,
time, mark, audio, video {
	margin: 0;
	padding: 0;
	border: 0;
	font-size: 100%;
	font: inherit;
	vertical-align: baseline;
}
/* HTML5 display-role reset for older browsers */
article, aside, details, figcaption, figure,
footer, header, hgroup, menu, nav, section {
	display: block;
}
body {
	line-height: 1;
}
ol, ul {
	list-style: none;
}
blockquote, q {
	quotes: none;
}
blockquote:before, blockquote:after,
q:before, q:after {
	content: '';
	content: none;
}
table {
	border-collapse: collapse;
	border-spacing: 0;
}
*, *:before, *:after {
 box-sizing: border-box;
}
html,body,#app,#main{
	height: 100%;
	width: 100%;
}
html {
  box-sizing: border-box;
}
#nav{
	height: 10%;
}
.component{
	padding: 15px;
}
#main{
	margin: 0;
	padding: 0;
}
a{
	margin-right: 5px;
}
.page{
	height: 90%;
}
```

**/package.json**

```json
{
  "name": "react-routing-boilerplate",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Andree DevelDoe Ray",
  "license": "ISC",
  "dependencies": {
    "axios": "^0.15.3",
    "express": "^4.14.1",
    "react": "^0.14.7",
    "react-dom": "^0.14.7",
    "react-router": "^3.0.2"
  },
  "devDependencies": {
    "babel-core": "^6.5.1",
    "babel-loader": "^6.2.2",
    "babel-preset-es2015": "^6.5.0",
    "babel-preset-react": "^6.5.0",
    "babel-preset-stage-0": "^6.22.0"
  }
}
```

**/server.js**

```js
var express = require('express'),
    app     = express()

app.use(express.static('public'))

app.listen(4000, function(){
    console.log('Express server is up on port: 4000')
})
```

**webpack.config.js**

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
            Main:        'app/components/Main',
            Nav:         'app/components/Nav',
            ComponentOne: 'app/components/ComponentOne/ComponentOne',
            Input:       'app/components/ComponentOne/Input',
            Output:       'app/components/ComponentOne/Output',
            ComponentTwo:'app/components/ComponentTwo/ComponentTwo',
            Api:         'app/api/api'
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
    },
    devtool: 'cheep-module-eval-source-map'
}
```

**/app/entry.jsx**

```js
var React = require('react'),
    ReactDOM = require('react-dom'),
    Main  = require('Main'),
    ComponentOne = require('ComponentOne'),
    ComponentTwo = require('ComponentTwo'),
    {Route, Router, IndexRoute, hashHistory} = require('react-router')


ReactDOM.render(
    <Router history={hashHistory}>
        <Route path="/" component={Main}>
            <IndexRoute component={ComponentOne}/>
            <Route path="componentwo" component={ComponentTwo}/>
        </Route>
    </Router>,
    document.getElementById('app')
)
```

**/app/components/nav.jsx**

```js
var React = require('react'),
    {Link, IndexLink} = require('react-router')

var Nav  = (props) => {
    return (
        <div className="component" id="nav" style={{border: '4px solid green',background:'rgba(0,255,0,0.1)'}}>
            <IndexLink activeClassName="active" activeStyle={{fontWeight:'bold'}} to="/">ComponentOne</IndexLink>
            <Link activeClassName="active" activeStyle={{fontWeight:'bold'}} to="/componentwo">ComponentTwo</Link>
        </div>
    )
}

module.exports = Nav
```

**/app/components/main.jsx**

```js
var React = require('react'),
    Nav = require('Nav')

var Main = (props) => {
    return (
        <div className="component" id="main" style={{border: '2px solid red'}}>
            <Nav/>
            {props.children}
        </div>
    )
}

module.exports = Main
```


