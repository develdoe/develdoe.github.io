---
date: '2017-10-27 07:27 +0200'
published: true
title: Git - Introduktion
---
*Den här artikeln är en översättning och i vissa fall ett försök att förkorta ned och förenkla orginal artikeln [Getting Started - About Version Control](https://git-scm.com/book/en/v2/Getting-Started-About-Version-Control)*

Den här artikeln handlar om att komma igång med Git. Vi börjar med att förklara lite bakgrund på verktyg för versionskontroll, sedan gå vidare till hur man får Git att köra på ditt system och slutligen hur du konfigurerar det för att börja arbeta med det. I slutet av denna artikel borde du förstå varför Git finns till, varför du bör använda Git, och du borde vara redo attt sätta igång.

## Om Version Control

Vad är "versionskontroll", och varför ska du bry dig? Versionskontroll är ett system som registrerar ändringar i en fil eller en uppsättning filer över tiden så att du kan återkalla specifika versioner senare. 

*I den här artikeln kommer du att använda källkod som exempel, men i verkligheten kan du göra det med nästan vilken typ av fil som helst på en dator.*

Om du är grafiker eller webbdesigner och vill behålla varje version av en bild eller layout, är ett Version Control System (VCS) ettt mycket smart sak att använda.

Det låter dig återställa valda filer tillbaka till ett tidigare tillstånd, återställa hela projektet tillbaka till ett tidigare tillstånd, jämföra förändringar över tid, se vem som senast gjort ändringar som kan ha orsaka ett problem eller som faktiskt introducerade ett problem och när han/hon gjorde det mm.