---
date: '2018-03-07 16:08 +0100'
published: true
title: Webpack - Koncept
---
Wwebpack är en statisk modulbuntare för moderna JavaScript-applikationer.

När webpack behandlar din applikation bygger den rekursivt en beroendegraf som innehåller alla moduler som din applikation behöver, och sedan paketerar alla dessa moduler i en eller flera buntar.

För att komma igång behöver du bara förstå fyra grundläggande begrepp:

* Entry
* Output
* Loaders
* Plugins

Detta dokument är avsett att ge en överblick över dessa begrepp på hög nivå,

## Entry

En entry anger vilket modulpaket som ska användas för att börja bygga upp det interna beroendet. Efter att ha kommit in i entry kommer webpack att ta reda på vilka andra moduler och bibliotek som entry beror på (direkt och indirekt). Varje beroende beräknas sedan och utmatas i filer som kallas buntar.

Du kan ange en entry (eller flera) genom att konfigurera entry segenskapen i webpackkonfigurationen.

**webpack.config.js**
```js
module.exports = {
    entry: './path/to/my/entry/file.js'
}
```

*Du kan konfigurera entry på olika sätt beroende på behoven hos din applikation*

## Output

Output berättar för webpack var buntarna hamnar och hur desssa ska namnges. Du kan konfigurera denna del av processen genom att ange ett `output` i din konfiguration:

**webpack.config.js**
```js
const path = require('path')

module.exports = {
    entry: './path/to/my/entry/file.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'my-first-webpack.bundle.js'
    }
}
```

I exemplet ovan använder vi `output.filename` och `output.path` för att berätta för webpack namnet på vårt paket och där vi vill att det ska skickas till.

## Loaders

Laddare gör det möjligt för webpack att bearbeta mer än bara JavaScript-filer (webpacken förstår bara JavaScript). De ger dig möjligheten att utnyttja webpacks samlingsfunktioner för alla typer av filer genom att konvertera dem till giltiga moduler som webpack kan hantera.

I huvudsak omvandlar webpack-laddare alla typer av filer till moduler som kan ingå i applikationenens beroendegraf (och så småningom en bundle).

*Observera att möjligheten att importera vilken typ av modul som helst, t.ex. .css-filer, är en funktion som är specifik för webpack och kanske inte stödjas av andra buntare eller uppgiftslöpare. Vi anser att denna förlängning av språket är motiverat eftersom det tillåter utvecklare att bygga en mer exakt beroendestruktur.*

På hög nivå har laddare två ändamål i din webpackkonfiguration:

1. `test` egenskapen identifierar vilken fil eller filer som ska transformeras.
2. `use` egenskapen anger vilken laddare som ska användas för att göra transformationen.

**webpack.config.js**
```js
const path = require('path')

const config = {
    entry: './path/to/my/entry/file.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'my-first-webpack.bundle.js'
    },
    module: {
        rules: [
            { test: /\.txt$/, use: 'raw-loader' }
        ]
    }
}

module.exports = config
```

Konfigurationen ovan har definierat en `rules` egenskap för en enda modul med två erforderliga egenskaper, `test` och `use`.

Detta berättar för webpacks kompilator följande:

> "Hej webpack-kompilator, när du stöter på en sökväg som löser en ".txt"-fil inuti ett `require()` / import-uttryck, använd raw-loader för att transformera den innan du lägger till den i bunten."

**Det är viktigt att komma ihåg att när du definierar regler i din webpack-konfiguration definierar du dem under module.rules och inte rules.**

*Det finns andra, mer specifika egenskaper att definiera på lastare som vi ännu inte har täckt.*

## Plugins

Medan loaders används för att omvandla vissa typer av moduler kan plugins användas för att utföra ett brett spektrum av uppgifter.

Plugins sträcker sig från buntoptimering och minimering, hela vägen till att definiera miljöliknande variabler.

För att kunna använda ett plugin måste du använda `require` och lägga till den till `plugins` arrayn. De flesta plugins kan anpassas via `options`. 

Eftersom du kan använda en plugin flera gånger i en config för olika ändamål måste du skapa en instans av den genom att anropa den med `new`.

**webpack.config.js**
```js
const HtmlWebpackPlugin = require('html-webpack-plugin') //installed via npm
const webpack = require('webpack') //to access built-in plugins
const path = require('path')

const config = {
    entry: './path/to/my/entry/file.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'my-first-webpack.bundle.js'
    },
    module: {
        rules: [
            { test: /\.txt$/, use: 'raw-loader' }
        ]
    },
    plugins: [
        new webpack.optimize.UglifyJsPlugin(),
        new HtmlWebpackPlugin({template: './src/index.html'})
    ]
}

module.exports = config
```

Det finns många plugins som webpack kommer med. Att använda plugins i din webpackkonfiguration är enkelt - men det finns många användarfall som är värda att undersöka ytterligare.
