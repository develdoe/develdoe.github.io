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

```
module.exports = {
    entry: './app/entry.jsx',
    output: {
        path: __dirname,
        filename: './public/js/bundle.js'
    },
    resolve: {
        root: __dirname,
        alias: {
            Main      : 'app/component/Main.jsx',
            Nav       : 'app/component/Nav.jsx',
            About     : 'app/component/About.jsx'
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

