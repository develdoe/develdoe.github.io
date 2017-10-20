---
date: '2017-10-20 15:39 +0200'
published: true
title: JavaScript - RequireJS
---
Att ladda in dina JavaScript-filer via flera `<script>` taggar är mycket enkelt, men att göra det på så sätt har många nackdelar, inklusive ökning av HTTP-överhead.

När du arbetar med webbapplikationer är det bra att dela upp din app i flera JS-filer, så det är enkelt att snabbt nå den gränsen. Detta kan negeras genom att du compilera din kod i en fil som en del av en byggprocess, men hjälper inte när skript laddas synkront. Det innebär att webbläsaren inte kan fortsätta måla sidan medan skriptet laddas.



