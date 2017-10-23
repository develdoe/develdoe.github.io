---
date: '2017-10-23 18:09 +0200'
published: true
title: Backbone - Fundamentals
---
Designmönster är bevisade lösningar på gemensamma utvecklingsproblem som kan hjälpa oss att förbättra organisationen och strukturen i våra applikationer. Genom att använda mönster, drar vi nytta av den kollektiva erfarenheten av skickliga utvecklare som upprepade gånger har löst liknande problem.

Historiskt sett har utvecklare som skapat desktop- och server-klass applikationer haft en mängd designmönster som de kan luta sig på, men det har bara under de senaste åren varit sådan att mönster har tillämpats på utveckling av front end.

I den här artikeln ska vi utforska utvecklingen av MVC-designmönstret och förstå hur Backbone.js tillåter oss att tillämpa detta mönster för front end utveckling.

## MVC

MVC är ett arkitektoniskt mönstret som uppmuntrar till förbättrad applikationsorganisation genom en separation av åtaganden. Det verkställer isoleringen av företagsdata (Models) från användargränssnitt (Views), med en tredje komponent (Controllers) som traditionellt hanterar logik, användarinmatning och samordning av Modeller och Visningar.

### Smalltalk-80 MVC

Det är viktigt att förstå de problem som det ursprungliga MVC-mönstret syftade till att lösa, eftersom det har förändrats ganska kraftigt sedan dess ursprungsdagar. På 70-talet var grafiska användargränssnitt få och långt ifrån varandra.

Ett tillvägagångssätt som kallas Separated Presentation började användas som ett medel för att göra en tydlig uppdelning mellan domänobjekt som modellerade koncept i den verkliga världen (t.ex. ett foto, en person) och de presenterade objekten som renderades på användarens skärm.

Smalltalk-80-implementeringen av MVC tog detta koncept vidare och hade ett syfte att separera ut programlogiken från användargränssnittet. Tanken var att en avkoppling mellan dessa delar av applikationen skulle tillåta återanvändning av Modeller för andra gränssnitt i applikationen. Det finns några intressanta punkter värt att notera om Smalltalk-80s MVC-arkitektur:

* Ett domänelement var känt som en Model och var okunnig om användargränssnittet (Views och Controllers)
* Presentationen togs hand om av View och Controller, men det var inte bara en enda View och Controller. Ett visningskontrollpar var nödvändigt för att varje element skulle visas på skärmen och det var ingen sann separation mellan dem.
* Controllerns roll i detta par var hantering av användarinmatning (som tangenttryck och klickhändelser) och göra något förnuftigt med dem
* Observer-mönstret användes för att uppdatera visningen när modellen ändrats

Utvecklare blir ibland överraskad när de lär sig att Observer-mönstret (idag vanligtvis implementerat som ett Publicerings/prenumerationssystem) inkluderades som en del av MVCs arkitektur för decennier sedan.

i Smalltalk-80s MVC, ser View and Controller båda modellen: när som helst modell ändras, reagerar visningarna. Ett enkelt exempel på detta är en applikation som stöds av aktiemarknadsdata - för att applikationen ska visa information i realtid bör ändringar i data i sin modell resultera i att uppdateringen uppdateras direkt.

### MVC Applied To The Web

Webben är beroende av HTTP-protokollet, vilket är statlöst. Det betyder att det inte finns en ständigt öppen anslutning mellan webbläsaren och servern. varje förfrågan instanserar en ny kommunikationskanal mellan de två.

När förfrågningsinitiatorn (t.ex. en webbläsare) får ett svar är anslutningen stängd. Detta faktum skapar ett helt annat sammanhang jämfört med ett av de operativsystem som många av de ursprungliga MVC-idéerna utvecklades. MVC-implementeringen måste anpassasig till webbkontexten.
