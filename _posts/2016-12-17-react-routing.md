---
date: '2016-12-17 15:37 +0100'
published: true
title: React - Routing
category:
  - React
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
            Main:     'app/components/Main.jsx',
            Nav:      'app/components/Nav.jsx',
            Index:    'app/components/Index.jsx',
            About:    'app/components/About.jsx',
            Examples: 'app/components/Examples.jsx'
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
var React = require('react'), ReactDOM = require('react-dom'),
    Main  = require('Main'), Index = require('Index'), About = require('About'), Examples = require('Examples'),
    {Route, Router, IndexRoute, hashHistory} = require('react-router')


ReactDOM.render(
    <Router history={hashHistory}>
        <Route path="/" component={Main}>
            <IndexRoute component={Index}/>
            <Route path="about" component={About}/>
            <Route path="examples" component={Examples}/>
        </Route>
    </Router>,
    document.getElementById('app')
)
```

**Main.jsx**

```js
var React = require('react'), Nav = require('Nav')

module.exports  = React.createClass({
    render: function () {
        return (
            <div className="component" id="main" style={{border: '2px solid red'}}>
                <Nav/>
                {this.props.children}
            </div>
        )
    }
})
```

**Nav.jsx**

```js
var React = require('react'), {Link, IndexLink} = require('react-router')

module.exports  = React.createClass({
    render: function () {
        return (
            <div className="component" id="nav" style={{border: '4px solid green',background:'rgba(0,255,0,0.1)'}}>
                <IndexLink activeClassName="active" activeStyle={{fontWeight:'bold'}} to="/">Get Weather</IndexLink>
                <Link activeClassName="active" activeStyle={{fontWeight:'bold'}} to="/about">About</Link>
                <Link activeClassName="active" activeStyle={{fontWeight:'bold'}} to="/examples">Examples</Link>
            </div>
        )
    }
})
```

**index.jsx**

```js
var React = require('react')

module.exports  = React.createClass({
    render: function () {
        return (
            <div className="component page" id="index" style={{border: '4px solid blue',background:'rgba(0,0,255,0.1)'}}>
                <span>index component</span>
            </div>
        )
    }
})
```

**about.jsx**

```js
var React = require('react')

module.exports  = React.createClass({
    render: function () {
        return (
            <div className="component page" id="about" style={{border: '4px solid blue',background:'rgba(0,0,255,0.1)'}}>
                <span>about component</span>
            </div>
        )
    }
})
```
