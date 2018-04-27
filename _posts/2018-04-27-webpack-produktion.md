---
date: '2018-04-27 08:46 +0200'
published: true
title: Webpack - Produktion
---
> Denna guide är en översättning från production sektionen av webpacks guide. Du hittar orginal artikeln [här](https://webpack.js.org/guides/production/)

I den här guiden dyker vi in i några av de bästa metoderna och verktygen för att bygga en produktionssite eller applikation.

### Setup


Målen för utveckling och produktion bygger skiljer sig mycket. Under utveckling vill vi ha stark källkarta och en localhost-server med live reloading eller hot module replacement. I produktionen övergår våra mål till fokus på minifierade buntar, kartor med lättare viktkälla och optimerade tillgångar för att förbättra laddningstiden. 

Med den här logiska separationen till hands rekommenderar vi att du skriver separata webpackskonfigurationer för varje miljö.

Medan vi kommer att skilja produktionen och utvecklingsspecifika bitar ut, noterar vi att vi fortfarande kommer att behålla en "vanlig" konfiguration för att hålla sakerna torra. För att sammanfoga dessa konfigurationer tillsammans använder vi ett verktyg som heter webpack-merge. 

Med den gemensamma konfigurationen på plats behöver vi inte duplicera kod inom de miljöspecifika konfigurationerna.

Låt oss börja med att installera webpack-merge och dela ut de bitar som vi redan har arbetat med i tidigare guider:

`npm i --save-dev webpack-merge`

*project* 
```
webpack-demo
  |- package.json
- |- webpack.config.js
+ |- webpack.common.js
+ |- webpack.dev.js
+ |- webpack.prod.js
  |- /dist
  |- /src
    |- index.js
    |- math.js
  |- /node_modules
```
*webpack.common.js*
```
const path = require('path')
const CleanWebpackPlugin = require('clean-webpack-plugin')
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = {
    entry: {
        app: './src/index.js'
    },
    plugins: [
        new CleanWebpackPlugin(['dist']),
        new HtmlWebpackPlugin({
            title: 'Production'
        })
    ],
    output: {
        filename: '[name].bundle.js',
        path: path.resolve(__dirname, 'dist')
    }
}
```
*webpack.dev.js*
```
const merge = require('webpack-merge')
const common = require('./webpack.common.js')

module.exports = merge(common, {
    devtool: 'inline-source-map',
    devServer: {
        contentBase: path.join(__dirname, "dist"),
        compress: false,
        inline: true,
        port: 3001
    }
})
```
*webpack.prod.js*
```

I webpack.common.js har vi nu vår inställning för `entry` och `output` konfigurerad och vi har inkluderat alla plugins som krävs för båda miljöerna.

I webpack.dev.js har vi lagt till den rekommenderade `devtool` för den miljön (stark källkartläggning), liksom vår enkla devServer-konfiguration.

Slutligen, i webpack.prod.js, inkluderade vi UglifyJSPlugin som minifierar  JavaScript.

Notera användningen av merge() i de miljöspecifika konfigurationerna för att enkelt inkludera vår gemensamma konfiguration i dev och prod.
