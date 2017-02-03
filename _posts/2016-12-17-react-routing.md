---
date: '2016-12-17 15:37 +0100'
published: true
title: React - Routing
category:
  - react
---
prerequisite: [basic react app](http://develdoe.com/2016/react-boilerplate/)

```bash
npm install --save react-router
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
            Main:  'app/components/Main.jsx',
            Nav:   'app/components/Nav.jsx',
            Index: 'app/components/Index.jsx',
            About: 'app/components/About.jsx'
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

**entry.js**

```js
var
React       = require('react'),
ReactDOM    = require('react-dom'),
Main        = require('Main'),
Index       = require('Index'),
About       = require('About'),
{Route, Router, IndexRoute, hashHistory} = require('react-router')


ReactDOM.render(
    <Router history={hashHistory}>
        <Route path="/" component={Main}>
            <IndexRoute component={Index}></IndexRoute>
            <Route path="about" component={About}></Route>
        </Route>
    </Router>,
    document.getElementById('app')
)
```

**Main.jsx**

```js
var
React = require('react'),
Nav   = require('Nav')

module.exports  = React.createClass({
    render: function () {
        return (
            <div className="component" id="main" >
                <span>main component</span>
                <Nav/>
                {this.props.children}
            </div>
        )
    }
})
```

**Nav.jsx**

```js
var
React = require('react')

module.exports  = React.createClass({
    render: function () {
        return (
            <div className="component" id="nav">
                <span>nav component</span>
            </div>
        )
    }
})
```

**index.jsx**

```js
var
React = require('react')

module.exports  = React.createClass({
    render: function () {
        return (
            <div className="component" id="index">
                <span>index component</span>
            </div>
        )
    }
})
```

**about.jsx**

```js
var
React = require('react')

module.exports  = React.createClass({
    render: function () {
        return (
            <div className="component" id="about">
                <span>about component</span>
            </div>
        )
    }
})
```
