---
date: '2018-01-10 11:27 +0100'
published: true
title: Webpack - Sass Compilering
---
Webpack packar din app så att du kan använda alla bibliotek som är tillgängliga i NPM direkt in i din front-end-kod.

## Vad är Webpack

Kort sagt är Webpack en modulbuntare. Det tar tillgångar (som javascript, HTML, bilder, typsnitt, CSS, ...) och packiterar dem till bundles som tillhandahållas till klienten. Några av Webpacks fantastiska funktioner:

* **Lazy loading** - Ladda bara buntar när du behöver dem.
* **Hot Module Reloading** - Instant uppdatering av komponenter utan att behöva uppdatera din applikation.
* **Hashing** - Används för cachebusting. Filnamn kommer automatiskt att konverteras till hashed-filnamn.
* **Source maps** - För enkel felsökning av minifierade versioner av din applikation.

## Setup

Göra `webpack` kommandot globalt tillgängligt.

```bash
npm install webpack -g
```

Installera webpack-dev-server, som gör att du kan använda webpack-dev-server globalt

```bash
npm install webpack-dev-server -g
```

### Skapa ett nytt node module projekt

Initera ett nytt npm projekt. 

```bash
mkdir {projektkatalog}
cd {projektkatalog}
npm init
```

*Dessa inställningar kan ändras i din `package.json` efteråt.*

### Skapa en ny bundle


*./index.html*

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>

    <div id="app"></div>
    <script src="bundle.js"></script>
```

*./src/app.js*

```js
console.log('I am running! Woohoo!')
document.getElementById('app').innerHTML = 'My app is working!'
```

Låt oss göra lite Webpack magi. Kör följande kommando:

```bash
webpack ./src/app.js bundle.js
```

Kommandot ovan kommer att ta tag i app.js och kompilera det till bundle.js som laddas i index.html.

## Webpack config fil

*./webpack.config.js*

```js
module.exports = {
    entry: "./src/app.js",
    output: {
        filename: "bundle.js"
    }
}
```

Nu när du vill kompilera din app kör du bara:

```bash
webpack
```
Nu slipper du skriva hela kommandot om och om igen. 

Webpack levereras med en funktion som kallas watch, `wepback -w`. Men du kan ange det som en inställning i config-filen. När du är inställd på true, kommer webpack lyssna efter ändringar i dina projektfiler och omkompilerar dina tillgångar direkt utan att du behöver göra någonting.

```js
module.exports = {
    entry: "./src/app.js",
    output: {
        filename: "bundle.js"
    },
    watch: true
}
```

Nu är det enda du behöver göra att köra `webpack` kommandot och webpack kommer automatiskt lyssna efter ändringar. 

```
webpack
```

## Dev Server

Nu när vi ställt in våra Webpack-inställningar är dags att ta en titt på den `webpack-dev-server` som vi har installerat tidigare. Att köra den här servern är lätt.

```bash
webpack-dev-server
```

Webpack följer nu dina filer och serverar din app via `http://localhost:8080`. Det sista vi vill göra är att uppdatera sidan automatiskt när en fil ändras.

Detta kan också göras i webpack.config.js

```js
module.exports = {
    entry: "./src/app.js",
    output: {
        filename: "bundle.js"
    },
    watch: true,
    devServer: {
        inline: true
    }
}
```







