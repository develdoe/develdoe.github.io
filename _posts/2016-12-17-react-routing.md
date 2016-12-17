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
        filename: './public/js/bundle.js'
    },
    resolve: {
        root: __dirname,
        alias: {
            Main    : 'app/component/Main.jsx',
            Nav     : 'app/component/Nav.jsx',
            Index   : 'app/component/Index.jsx',
            About   : 'app/component/About.jsx'
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
var React                                 = require('react'),
    ReactDOM                              = require('react-dom'),
    {Route,Router,IndexRoute,hashHistory} = require('react-router'),
    Main = require('Main'), Index = require('Index'), About = require('About')


ReactDOM.render(
    <Router history={hashHistory}>
        <Route path="/" component={Main}>
            <Route path="about" component={About}></Route>
            <IndexRoute component={Index}></IndexRoute>
        </Route>
    </Router>,
    document.getElementById('app')
)
```

**Main.jsx**

```js
var React = require('react'),
    Nav   = require('Nav')

module.exports = React.createClass({
    render: function(){
        return (
            <div className="component main" style={{border:'1px solid red'}}> main component
                <Nav/>
                {this.props.children}
            </div>
        )
    }
})
```

**Nav.jsx**

```js
var React = require('react'),
    {Link, IndexLink} = require('react-router')

module.exports = React.createClass({
    render: function(){
        return (
            <div className="component about" style={{border:'1px solid green'}}>nav component
                <IndexLink activeClassName="active" to="/">index</IndexLink>
                <Link activeClassName="active" to="about">about</Link>
            </div>
        )
    }
})
```

**index.jsx**

```js
var React = require('react')

module.exports = React.createClass({
    render: function(){
        return (
            <div className="component index" style={{border:'1px solid blue'}}>index component</div>
        )
    }
})
```

**about.jsx**

```js
var React = require('react')

module.exports = React.createClass({
    render: function(){
        return (
            <div className="component about" style={{border:'1px solid blue'}}>about component</div>
        )
    }
})
``` 

