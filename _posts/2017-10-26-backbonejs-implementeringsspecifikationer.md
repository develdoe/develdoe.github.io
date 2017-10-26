---
date: '2017-10-26 19:20 +0200'
published: true
title: BackboneJS - Implementeringsspecifikationer
---
Ett SPA laddas i webbläsaren med en vanlig HTTP-begäran och svar. Sidan kan helt enkelt vara en HTML-fil eller det kan vara en vy konstruerad av en MVC-implementering på serverns sida.

När första sidan väl är laddad, avlyssnar en klient Router URL-adresser och åberopar logik på klienten istället för att skicka en ny begäran till servern. Bilden nedan visar typisk förfråganhantering för klient MVC som implementeras av BackboneJS:

![Backbone MVC](https://addyosmani.com/backbone-fundamentals/img/backbone_mvc.png)

URL-routing, DOM-händelser (t ex musklick) och modellhändelser (t ex attributändringar) utlöser alla utlösningslogik i Vyn. Hanterarna uppdaterar DOM och Modeller, vilket kan utlösa ytterligare händelser. Modeller synkroniseras med datakällor vilket kan innebära att 
de kommunicera med back-end-servrar.
