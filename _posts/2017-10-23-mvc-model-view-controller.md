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