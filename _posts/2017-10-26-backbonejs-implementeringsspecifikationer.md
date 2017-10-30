---
date: '2017-10-26 19:20 +0200'
published: true
title: BackboneJS - Implementeringsspecifikationer
---
*Denna artikel är en översättning och i viss mån försök till förkortning av kapitlet [Implementation Specifics](https://addyosmani.com/backbone-fundamentals/#backbone-basics).

Ett SPA laddas i webbläsaren med en vanlig HTTP-begäran och svar. Sidan kan helt enkelt vara en HTML-fil eller det kan vara en vy konstruerad av en MVC-implementering på serverns sida.

När första sidan väl är laddad, avlyssnar en klient Router URL-adresser och åberopar logik på klienten istället för att skicka en ny begäran till servern. Bilden nedan visar typisk förfråganhantering för klient MVC som implementeras av BackboneJS:

![Backbone MVC](https://addyosmani.com/backbone-fundamentals/img/backbone_mvc.png)

URL-routing, DOM-händelser (t ex musklick) och modellhändelser (t ex attributändringar) utlöser alla utlösningslogik i Vyn. Hanterarna uppdaterar DOM och Modeller, vilket kan utlösa ytterligare händelser. Modeller synkroniseras med datakällor vilket kan innebära att 
de kommunicera med back-end-servrar.

#### Models

* De inbyggda egenskaperna hos modeller varierar över ramverk. Det är emellertid vanligt att de stöder validering av attribut, där attribut representerar egenskaperna hos modellen, till exempel en modellidentifierare.
* När vi använder Modeller i verkliga applikationer behöver vi i allmänhet också ett sätt att fortbestå Modeller. Persistens tillåter oss att redigera och uppdatera Modellerna med vetskap om att deras senaste stater kommer att sparas någonstans, till exempel i en webbläsares localStorage eller synkroniserad med en databas.
* En modell kan ha flera vyer som observerar det de ändringar. Genom att observera menar vi att en View har ett registrerat intresse om att bli informerad när en uppdatering görs till modellen. Detta gör att View kan se till att det som visas på skärmen hålls synkroniserat med data som finns i modellen. Beroende på dina krav kan du skapa en enda vy som visar alla modellattribut eller skapa separata vyer som visar olika attribut. Den viktiga punkten är att modellen inte bryr sig om hur dessa vyer är organiserade, det meddelar helt enkelt uppdateringar av dess data som behövs genom ramverkets händelsessystem.
* Det är inte ovanligt för moderna MVC/ MV* -ramverk att skapa ett sätt att gruppera Modeller tillsammans. I Backbone heter dessa grupper kollektion. Genom att hantera modeller i grupper kan vi skriva applikationslogik baserat på meddelanden från gruppen när en modell inom gruppen ändras. Detta undviker behovet att manuellt observera enskilda modellinstanser. Vi ser det här i åtgärd senare i boken. Samlingar är också användbara för att utföra några aggregerade beräkningar över flera modeller.

#### Views

* Användare interagerar med Vyer, vilket vanligtvis betyder att läsa och redigera modelldata. Till exempel, i vår [Todo-applikation](/2017/backbonejs-client-side-mvc-style/), händer Todo Model visning i användargränssnittet i listan över alla Todo-objekt. Inom den är varje Todo gjord med sin titel och avslutad kryssruta. Modellredigering görs genom en "edit" Vy där en användare som har valt en specifik Todo redigerar sin titel i en form.
* Vi definierar ett `render()` verktyg inom vår vy som är ansvarig för att rendera innehållet i modellen med hjälp av en JavaScript-templerande motor (tillhandahållen av Underscore.js) och uppdatering av innehållet i vår vy, refererad av this.$el.
* Vi lägger sedan till vår `render()` callback som modellabonnent, så View kan utlösas för att uppdateras när modellen ändras.
* Du kanske undrar var användarinteraktion kommer till spel här. När användare klickar på ett Todo-element i vyn är det inte vyns ansvaret att veta vad den ska göra här näst. En Controller fattar detta beslut. I Backbone uppnås detta genom att lägga till en händelse lyssnare till Todos elementet som delegerar hanteringen av klicket till en händelsehanterare.

### Templating

I samband med JavaScript-ramverk som stöder MVC/MV * är det värt att titta närmare på JavaScript-templering och dess relation till Vyer.

Det har länge ansetts vara dålig praxis (och beräkningsmässigt dyrt) för att manuellt skapa stora block av HTML-markup i minnet genom strängkonstruktion.Utvecklare som använder den här tekniken ser sig själva ofta iterera genom sin data, sätter in den i in-kapslade divs och använder föråldrade tekniker såsom `document.write` för att injicera "mallen" i dess DOM. Detta tillvägagångssätt innebär ofta att man harin skriptmarkering inline med standardmarkup, vilket snabbt kan bli svårt att läsa och underhålla, särskilt vid uppbyggnad av stora applikationer.

JavaScript-templerande bibliotek (som mustasch eller Handlebars.js) används ofta för att definiera mallar för vyer som HTML-markup som innehåller mallvariabler. Dessa mallblock kan antingen lagras externt eller inom skript taggar med en anpassad typ (t ex 'text /mall'). Variabler är avgränsade med en variabel syntax (t.ex. `<% = title%>` för Underscore och `{%raw%}{{title}}{%endraw%}` för Handlebars).

JavaScript-templerings bibliotek accepterar vanligtvis data i ett antal format, inklusive JSON; ett serialiseringsformat som alltid är en sträng. Grov arbetet med att fylla mallar med data tas i allmänhet hand av ramverket själv. Detta har flera fördelar, särskilt när man väljer att lagra mallar externt vilket gör det möjligt för applikationer att ladda mallar dynamiskt på en nödvändig basis.

Låt oss jämföra två exempel på HTML-templering. En implementeras med det populära Handlebars.js-biblioteket och den andra använder Underscore's "microtemplates".

#### Handlebars.js

```html
<div class="view">
  <input class="toggle" type="checkbox" {{#if completed}} checked {{/if}}>
  <label>{{title}}</label>
  <button class="destroy"></button>
</div>
<input class="edit" value="{{title}}">
```

#### Underscore.js Microtemplates:

```html
<div class="view">
  <input class="toggle" type="checkbox" <%= completed ? 'checked' : '' %>>
  <label><%= title %></label>
  <button class="destroy"></button>
</div>
<input class="edit" value="<%= title %>">
```

Du kan också använda dubbla lockiga parenteser (dvs {{}}) (eller annan märkning du känner dig bekväm med) i Microtemplates. När det gäller häftiga hakparentes kan detta göras genom att ange Underscore templateSettings attributet enligt följande:

```js
_.templateSettings = { interpolate : /\{\{(.+?)\}\}/g };
```

### En notice om navigering och state

Det är också värt att notera att i klassisk webbutveckling krävde navigering mellan oberoende visningar användningen av en siduppdatering. I en single-page JavaScript-applikationer, hämtas data från en server via Ajax som kan dynamiskt återges i en ny vy på samma sida. Eftersom detta inte uppdaterar webbadressen automatiskt, faller rollen för navigering sålunda till en "router", som hjälper till att hantera applikationstillstånd (t.ex. tillåter användare att bokmärke en viss vy de har navigerat till).


