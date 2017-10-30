---
date: '2017-10-30 14:54 +0100'
published: true
title: BackboneJS - Basics
---
I det här avsnittet lär du dig det grundläggande om Backbone-modeller, vyer, kollektioner, händelser och routrar. Detta är inte på något sätt en ersättning för den officiella dokumentationen, men det hjälper dig att förstå många av de grundläggande begreppen bakom Backbone innan du börjar bygga applikationer som använder den.

Innan vi dyker in i fler kodexemplar, låt oss definiera en viss boilerplattform som du kan använda för att ange vilka beroenden som Backbone kräver. Denna pannplatta kan återanvändas på många sätt med liten eller ingen ändring och låter dig köra kod från exempel med lätthet.

Du kan klistra in följande i din textredigerare, och ersätta kommentaren mellan skriptet med JavaScript från ett visst exempel:

```html
<!DOCTYPE HTML>
<html>
<head>
    <meta charset="UTF-8">
    <title>Backbone Boilerplate</title>
</head>
<body>

<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js"></script>
<script src="http://underscorejs.org/underscore-min.js"></script>
<script src="http://backbonejs.org/backbone-min.js"></script>
<script>
  // Your code goes here
</script>
</body>
</html>
```
Du kan sedan spara och köra filen i din webbläsare, t.ex. Chrome eller Firefox. Alternativt, om du föredrar att arbeta med en online-kodredigerare, är jsFiddle och jsBin-versioner av den här kedjan också tillgängliga.
