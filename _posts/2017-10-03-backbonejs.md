---
date: '2017-10-03 12:07 +0200'
published: true
title: BackboneJS
---

I ett ekosystem där övergripande, beslutar-allt-för-dig-ramverk är vanliga, och många bibliotek kräver att din webbplats omorganiseras för att passa deras utseende, känsla och standardbeteende. Vill Backbone vara ett verktyg som ger dig frihet hur du utformar din webbapplikation.

Filosofiskt är Backbone ett försök att upptäcka de minimala uppsättningarna av datastrukturering (modeller och kollektioner) och användargränssnitt (vyer och URL:er) primitiva som är vanligtvist användbara vid uppbyggnad av webbapplikationer med JavaScript.

Backbone kopplar allt till ditt befintliga API över ett RESTful JSON-gränssnitt.

### Beroenden
* Underscore.js (> = 1.8.3)
* För RESTful persistens och DOM manipulation (Backbone.View), jQuery (> = 1.11.0) 
* för äldre Internet Explorer support, json2.js

## Getting Started
Med Backbone representerar du dina data som Modeller, dessa kan skapas, förstöras, valideras och sparas på servern. 

När en UI-åtgärd orsakar att en modell av en modell ändras, utlöser modellen en "förändring" -event; alla synpunkter som visar modellens tillstånd kan meddelas om ändringen, så att de kan svara i enlighet med det, återförvisa sig med den nya informationen. I en färdig Backbone-app behöver du inte skriva den limkod som tittar på DOM för att hitta ett element med ett visst ID och uppdatera HTML manuellt. När modellen ändras uppdateras vyerna.

## Models and Views


![models and views](http://backbonejs.org/docs/images/intro-model-view.svg)

Den viktigaste sak som Backbone kan hjälpa dig med är att hålla din affärslogik separat från ditt användargränssnitt.

När de två är intrasslade är förändring svår; När logiken inte är beroende av användargränssnitt blir ditt gränssnitt lättare att arbeta med.

Designa dina modeller som minimala återanvändbara objekt som innehåller alla användbara funktioner för att manipulera deras speciella bit data.

#### Model

En modell hanterar en intern tabell med dataattribut och utlöser "change" event när någon av dess data ändras.

Modeller hanterar synkroniseringsdata med ett persistenslager, vanligtvis ett REST API med en databas. 

* Orkesterar data och affärslogik.
* Laddar och sparar från servern.
* Utsänder events när data ändras.

#### View

En Vy är en minimal del av användargränssnittet. Den renderar ofta data från en viss modell eller antal modeller.

Men en Vy kan också vara data-less delar av användargränssnitt som står oberoende.

Modeller ska i allmänhet vara omedvetna om Vyer. Istället lyssnar Vyer på modellen "change" events och reagerar eller återställer sig på lämpligt sätt.

* Listens for changes and renders UI.
* Hanterar användarinmatning och interaktivitet.
* Skickar infångad input till modellen.

## Collections

![collections](http://backbonejs.org/docs/images/intro-collections.svg)

En kollektion hjälper dig att hantera en grupp relaterade modeller.

En kollektion hanterar laddning och lagring av nya modeller till servern och tillhandahåller hjälparfunktioner för att utföra aggregeringar eller beräkningar mot en lista av modeller.

Förutom att kollektioner har egna events, så lyssnar man även på modellernas egna events, vilket tillåter dig att lyssna efter event på alla modeller som tillhör kollektionen på ett och samma ställe.

## API Integration

Backbone är förkonfigurerad för att synkronisera med ett RESTful API. Skapa helt enkelt en ny kollektion med dess URL för en resursändpunkt.

```js
var Books = Backbone.Collection.extend({
  url: '/books'
});
```

Kollektion- och modellkomponenterna bildar tillsammans en direkt kartläggning av REST-resurser med hjälp av följande metoder:

```js
GET  /books/ .... collection.fetch();
POST /books/ .... collection.create();
GET  /books/1 ... model.fetch();
PUT  /books/1 ... model.save();
DEL  /books/1 ... model.destroy();
```

När du hämtar råa JSON-data från ett API, kommer en kollektion automatiskt att populeras med data formaterad som en array, medan en modell automatiskt fyller sig med data formaterad som ett objekt:

```js
[{"id": 1}] ..... populates a Collection with one model.
{"id": 1} ....... populates a Model with one attribute.
```

Det är dock ganska vanligt att stöta på API: er som returnerar data i ett annat format än vad Backbone förväntar sig. Som exempel, föreställ dig att du hämta en kollektion från ett API som returnerar den verkliga datarrayen som är inbäddad i metadata:

```js
{
  "page": 1,
  "limit": 10,
  "total": 2,
  "books": [
    {"id": 1, "title": "Pride and Prejudice"},
    {"id": 4, "title": "The Great Gatsby"}
  ]
}
```

I ovanstående exempeldata borde en kollektion populeras med användning av "books"-raktionen istället för rotobjektstrukturen. Denna skillnad är lätt att förena med en analysmetod som returnerar (eller omvandlar) önskad del av API-data:

```js
var Books = Backbone.Collection.extend({
  url: '/books',
  parse: function(data) {
    return data.books;
  }
});
```

## View Rendering

![view rendering](http://backbonejs.org/docs/images/intro-views.svg)

Varje vy hanterar rendering och användarinteraktion inom sitt eget DOM-element.

Om du är strikt om att inte tillåta vyer att nå utanför sig, hjälper det att hålla gränssnittet flexibelt, så att vyer kan göras isolerat på någon plats där de kan behövas.

Backbone har ingen opinion ang processen som används för att göra vy objekt och deras objekt till användargränssnittet. Du definierar hur dina modeller blir översatta till HTML (eller SVG eller Canvas eller något mer exotiskt). Det kan vara så prosaiskt som en enkel Underscore-mall, eller så snyggt som React Virtual DOM. Några grundläggande sätt att återge vyer finns i Backbone primer.

## Routing with URLs

![routing](http://backbonejs.org/docs/images/intro-routing.svg)

I rika webbapplikationer vill vi fortfarande tillhandahålla länkar, bokmärkningsbara och delbara webbadresser till meningsfulla platser i en app. 

Använd routern för att uppdatera webbläsarens webbadress när användaren når en ny "plats" i din app som de kanske vill bokmärka eller dela.

Omvänt upptäcker routern ändringar i webbadressen, säg att de trycker på "Tillbaka" -knappen, och kan diregera i applikationen beroende på vart du nu är.
