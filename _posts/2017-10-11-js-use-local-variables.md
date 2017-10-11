---
date: '2017-10-11 13:02 +0200'
published: true
title: JS - Use Local Variables
---
Lokala variabler är överlägset de snabbaste identifierarna både att läsa från och skriva till i JavaScript.

Eftersom de existerar i aktiveringsobjektet för exekveringsfunktionen, innefattar identifieringsupplösning inspektion av ett enda objekt i omfattningskedjan.

Mängden tid som är nödvändig för att läsa värdet av en variabel ökar med varje steg längs omfångskedjan, så ju större identifieringsdjupet desto långsammare kommer åtkomsten att vara.

Denna effekt kan ses i varje webbläsare förutom Google Chrome med v8 och Safari 4+ med hjälp av Nitro JavaScript-motorn, vilka båda är så snabba att identifieringsdjupet har liten inverkan på åtkomsthastigheten.
