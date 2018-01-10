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




