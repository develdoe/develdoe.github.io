---
date: '2017-10-23 17:37 +0200'
published: true
title: MVC - Model View Controller
---
Ett antal moderna JavaScript-ramverk ger utvecklare en enkel väg att organisera sin kod med hjälp av variationer av ett mönster som kallas MVC (Model-View-Controller). MVC skiljer intressen i en applikation i tre delar:

* **Models** - representerar domänspecifik kunskap och data i en applikation. Tänk på det här som en "typ" av data som du kan modellera - som en användar-, foto- eller att göra notering. modeller kan meddela observatörer när deras tillstånd ändras.
* **Views** - utgör typiskt användargränssnittet i en applikation (t.ex. markup och mallar), men behöver inte vara. De observerar modeller, men kommunicerar inte direkt med dem.
* **Controlers** - hanterar inmatning (t ex klick, användaråtgärder) och uppdatera modeller.

I en MVC-applikation påverkas användarinmatning således av Controllers som uppdaterar Modeller. Vyer observera Modeller och uppdatera användargränssnittet när förändringar uppstår.

JavaScript MVC-ramverk följer inte alltid strikt ovanstående mönster. Vissa lösningar sammanfogar Controllerens ansvar i View, medan andra tillvägagångssätt lägger till ytterligare komponenter i mixen.

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

Ett exempel på en serverns webbapplikationsramverk som försöker tillämpa MVC på webbkontexten är Ruby On Rails.

![RoR](https://addyosmani.com/backbone-fundamentals/img/rails_mvc.png)

Kärnpunkten är de tre MVC-komponenterna vi förväntar oss - modell-, vy- och kontrollörarkitekturen. I Rails:

* Modeller representerar data i en applikation och används vanligtvis för att hantera regler för att interagera med en viss databastabell. Du har vanligtvis ett bord som motsvarar en modell med mycket av din ansökans affärslogik som lever i dessa modeller.
* Vyer representerar ditt användargränssnitt, ofta i form av HTML som skickas ner till webbläsaren. De används för att presentera applikationsdata till allt som gör begäran från din applikation.
* Controllers erbjuder limet mellan modeller och visningar. Deras ansvar är att bearbeta förfrågningar från webbläsaren, fråga dina modeller om data och ge sedan erbjuda denna datan till vyer så att de kan presenteras för webbläsaren.

Även om det finns en tydlig separation av intresse som är MVC-liknande i Rails, använder det faktiskt ett annat mönster som heter Model2. En orsak till detta är att Rails inte meddelar vyer från modellen eller controllers, det passar bara modelldata direkt till vyn.

Med detta sagt, även om serverns arbetsflöde för att ta emot en begäran från en webbadress, bakar du ut en HTML-sida som svar och skiljer din affärslogik från gränssnittet har många fördelar. På samma sätt som att hålla ditt användargränssnitt skilt från dina databasregistreringar är användbart i server-side ramverk, är det lika användbart att hålla din användargränssnitt ren separerad från dina datamodeller i JavaScript.

Olika implementeringar av server-side av MVC (som PHP Zend ramverket) implementerar också Front Controller-designmönstret. Det här mönstret lagrar en MVC stack bakom en enda entry punkt. Den här enskilda ingångspunkten innebär att alla HTTP-förfrågningar (t.ex. `http://www.example.com`, `http://www.example.com/whicheverpage/`, etc.) dirigeras av serverns konfiguration till samma handler, oberoende av URI.

När Front Controllern mottar en HTTP-förfrågan analyserar den den och bestämmer vilken klass (Controller) och Method (Action) som ska åberopas. Den valda Controller Action tar över och interagerar med lämplig modell för att uppfylla begäran. Controller tar emot data tillbaka från modellen, laddar in en lämplig vy, injicerar modelldata i den och returnerar svaret till webbläsaren.

Låt oss till exempel säga att vi har vår blogg på `www.example.com` och vi vill redigera en artikel (med `id=43`) och begära `http://www.example.com/article/edit/43:`

På serverns sida skulle Front Controller analysera webbadressen och åberopa artikelkontrolleraren (motsvarande `/article/` delen av URI) och dess Redigerings Action (som motsvarar `/edit/` delen av URI). I den Action skulle det finnas ett anrop till, låt oss säga Article Model och dess `Article::getEntry(43)` metod (43 motsvarar `/43` i slutet av URI). Detta skulle returnera bloggartikeldata från databasen för redigering. Article Controller skulle sedan ladda (`article/Edit`) View som skulle inkludera logik för att injicera artikelns data i en form som är lämplig för att redigera dess innehåll, titel och andra (meta) data. Slutligen skulle det resulterande HTML-svaret returneras till webbläsaren.

Som ni kan föreställa er ett liknande flöde nödvändigt med POST-förfrågningar efter att vi trycker på en spara-knapp i en form.

POST-åtgärdens URI skulle se kunna se ut så här, `/article/save/43`. Förfrågan skulle gå igenom samma kontroller, men den här gången skulle Save Action åtgärden åberopas (på grund av `/save/` URI-delen), Article Model skulle spara den redigerade artikeln i databasen med `Articles::saveEntry(43)` och webbläsaren skulle omdirigeras till `/article/edit/43` URI för vidare redigering.



