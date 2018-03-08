---
date: '2018-03-08 14:22 +0100'
published: true
title: Karma - Konfiguration
---
Karma behöver veta om ditt projekt för att testa det och det görs via en konfigurationsfil.

Se [konfigurationsfildokument](https://karma-runner.github.io/2.0/config/configuration-file.html) för mer information om syntaxen och alla tillgängliga alternativ.

## Genererar config filen

konfigurationsfilen kan genereras med `karma init`:

```bash
$ karma init my.conf.js

Which testing framework do you want to use?
Press tab to list possible options. Enter to move to the next question.
> jasmine

Do you want to use Require.js?
This will add Require.js plugin.
Press tab to list possible options. Enter to move to the next question.
> no

Do you want to capture a browser automatically?
Press tab to list possible options. Enter empty string to move to the next question.
> Chrome
> Firefox
>

What is the location of your source and test files?
You can use glob patterns, eg. "js/*.js" or "test/**/*Spec.js".
Press Enter to move to the next question.
> *.js
> test/**/*.js
>

Should any of the files included by the previous patterns be excluded?
You can use glob patterns, eg. "**/*.swp".
Press Enter to move to the next question.
>

Do you want Karma to watch all the files and run the tests on change?
Press tab to list possible options.
> yes

Config file generated at "/Users/vojta/Code/karma/my.conf.js".
```

Konfigurationsfilen kan också skrivas i CoffeeScript. Faktum är att om du kör `karma init` med en `*.coffee` förlängning som `karma init karma.conf.coffee`, kommer det att generera en CoffeeScript-fil.

Självklart kan du skriva konfigurationsfilen manuellt eller kopiera-klistra in den från ett annat projekt.

## Starta Karma

När du startar Karma kan konfigurationsfilens path skickas in som första argumentet. 

Som standard kommer Karma leta efter `karma.conf.js` eller `karma.conf.coffee` i den aktuella katalogen.

```bash
# Start Karma using your configuration:
$ karma start my.conf.js
```