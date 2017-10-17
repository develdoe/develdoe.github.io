---
date: '2017-10-03 12:07 +0200'
published: true
title: BackboneJS
---

Backbone.js ger strukturen till webbapplikationer.

I ett ekosystem där övergripande, beslutar-allt-för-dig-ramverk är vanliga, och många bibliotek kräver att din webbplats omorganiseras för att passa deras utseende, känsla och standardbeteende. Vill Backbone vara ett verktyg som ger dig frihet hur du utformar din webbapplikation.

Filosofiskt är Backbone ett försök att upptäcka de minimala uppsättningarna av datastrukturering (modeller och kollektioner) och användargränssnitt (vyer och URL:er) primitiva som är vanligtvist användbara vid uppbyggnad av webbapplikationer med JavaScript.

* Den tillhandahåller modeller med nyckel-värde par bindningar  
* Anpassade händelsehanteringar
* Kollektioner med ett rikt API av otaliga funktioner
* Vyer med deklarativ händelsehantering 

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



