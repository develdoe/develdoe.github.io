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

```bash
npm install webpack -g
```

