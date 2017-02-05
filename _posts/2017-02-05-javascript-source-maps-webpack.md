---
date: '2017-02-05 13:25 +0100'
published: true
title: JavaScript - Source Maps & webpack
category:
  - JavaScript
---
Keep your client-side code readable and more importantly debuggable even after you've combined and minified it.

To use source map with Webpack add the following line to your webpack config, ```devtool: 'cheep-module-eval-source-map'```, so that your configuration file looks something like this:

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
            Main:     'app/components/Main',
            Nav:      'app/components/Nav',
            Weather:    'app/components/Weather',
            About:    'app/components/About',
            Examples: 'app/components/Examples',
            WeatherForm: 'app/components/WeatherForm',
            WeatherMessage: 'app/components/WeatherMessage',
            openweathermap: 'app/api/openweathermap'
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
