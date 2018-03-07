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