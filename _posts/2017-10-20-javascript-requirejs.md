---
date: '2017-10-20 15:39 +0200'
published: true
title: JavaScript - RequireJS
---
Att ladda in dina JavaScript-filer via flera `<script>` taggar är mycket enkelt, men att göra det på så sätt har många nackdelar, inklusive ökning av HTTP-överhead.

När du arbetar med webbapplikationer är det bra att dela upp din app i flera JS-filer, så det är enkelt att snabbt nå den gränsen. Detta kan negeras genom att du compilera din kod i en fil som en del av en byggprocess, men hjälper inte när skript laddas synkront. Det innebär att webbläsaren inte kan fortsätta måla sidan medan skriptet laddas.

Verktyg som RequireJS gör så att script laddas asynkront. Detta betyder att du måste anpassa din kodbas något. Du kan inte bara byta ut `<script>` element för en liten bit av RequireJS-kod, men fördelarna är värdefulla:

* Laddar skriptna asynkront betyder att lastprocessen är icke-blockerande. Webbläsaren kan fortsätta måla upp resten av sidan när skriptna laddas, vilket gör att den första laddningstiden påskyndas.
* Vi kan ladda modulerna mer intelligent, ha mer kontroll över när de laddas och se till att moduler som har beroenden laddas i rätt ordning.

## Behov av bättre beroendehantering

Dependenshantering är ett utmanande ämne, i synnerhet när du skriver JavaScript i webbläsaren.

Det närmaste som vi måste beredskapshantering som standard är helt enkelt att se till att vi beställer våra `<script>` taggar så att kod som beror på kod i en annan fil laddas efter den fil det beror på.


