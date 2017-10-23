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

Slutligen, om användaren begärde http://www.example.com/ skulle Front Controller anropa standard Controller och Action; t.ex. Index Controllern och dess Index-action. Inom Index Action skulle det finnas ett anrop till Araticles-modellen och dess `Articles::getLastEntries(10)` metod som skulle returnera de senaste 10 blogginläggen. Controllern skulle ladda blog/index vyn som skulle ha grundläggande logik för att lista bloggposterna.

Bilden nedan visar denna typiska HTTP-förfrågan / svarlivscykel för server-sida MVC:

![svarlivscykel för server-sida MVC:](https://addyosmani.com/backbone-fundamentals/img/webmvcflow_bacic.png)

Servern tar emot en HTTP-förfrågan och routrar den via en enda ingångspunkt. Vid den ingången analyserar Front Controller förfrågan och baserat på den, åberopas en Action av aktuell Controller. Denna process kallas routing. Action modellen uppmanas att returnera och/eller spara inlämnade data. Modellen kommunicerar med datakällan (t.ex. databas eller API). När Model slutfört sitt arbete returnerar den data till Controllern som sedan laddar in lämplig Vy. Viewen kör presentationslogik, loopar genom artiklar och utskrifter, innehåll, etc, med hjälp av den medföljande data. I slutändan returneras ett HTTP-svar till webbläsaren.

### Client-Side MVC & Single Page Apps

Flera studier har bekräftat att förbättringar av latens kan ha en positiv inverkan på användningen och användarengagemanget för webbplatser och appar. Detta står i motsats till det traditionella tillvägagångssättet för webbapputveckling, vilket är mycket servercentrerat, vilket kräver en fullständig sidåterladdning för att flytta från en sida till nästa. Även med tung cache på plats måste webbläsaren fortfarande analysera CSS, JavaScript och HTML och rendera gränssnittet till skärmen.

Förutom att resultera i att mycket duplicerat innehåll serveras tillbaka till användaren, påverkar detta tillvägagångssätt både latens och allmän upplevelse av användarupplevelsen. En trend för att förbättra uppfattad latens under de senaste åren har varit att gå vidare mot att bygga s.k. Single Page Applications (SPA) - appar som efter en första sidbelastning kan hantera efterföljande navigeringar och begäran om data utan att behöva helt ladda om sidan.

När en användare navigerar till en ny vy begärs ytterligare innehåll för visningen med hjälp av en XHR (XMLHttpRequest), som typiskt kommunicerar med ett REST API eller slutpunkt på serverns endpoint. Ajax (Asynkron JavaScript och XML) gör kommunikation med servern asynkron så att data överförs och bearbetas i bakgrunden, vilket gör att användaren kan interagera med andra delar av en sida utan avbrott. Detta förbättrar användbarhet och lyhördhet.

SPAs kan också dra nytta av webbläsarfunktioner som History API för att uppdatera adressen som ses i platsfältet när du flyttar från en vy till en annan. Dessa webbadresser gör det också möjligt att bokmärke och dela ett visst programstatus utan att behöva navigera till helt nya sidor.

Det typiska SPA består av mindre bitar av gränssnitt som representerar logiska enheter, som alla har egna användargränssnitt, affärslogik och data. Ett bra exempel är en korg i en shopping webbapplikation som kan ha föremål tillagt. Denna korg kan presenteras för användaren i en ruta längst upp till höger på sidan (se bilden nedan):

![smaller pieces of interface](https://addyosmani.com/backbone-fundamentals/img/wireframe_e_commerce.png)

Korgen och dess data presenteras i HTML. Data och dess associerade View i HTML ändras över tiden. Det fanns en tid när vi använde jQuery (eller ett liknande DOM-manipulationsbibliotek) och en massa Ajax-samtal och återuppringningar för att hålla de två synkroniserade. Det skapade ofta kod som inte var välstrukturerad eller lätt att underhålla. Buggar var frekventa och kanske oundvikliga.

Behovet av snabba, komplexa och lyhörda Ajax-drivna webbapplikationer kräver replikering av mycket av denna logik på klientsidan, vilket dramatiskt ökar storleken och komplexiteten hos koden som finns där. Så småningom har det lett till att vi behöver MVC (eller en liknande arkitektur) som implementeras på klient sidan för att bättre strukturera koden och underlätta att underhålla och vidareutveckla under applikationens livscykel.


