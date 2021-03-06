---
date: '2017-10-26 19:20 +0200'
published: true
title: BackboneJS - Implementeringsspecifikationer
---
*Denna artikel är en översättning och i viss mån försök till förkortning av kapitlet [Implementation Specifics](https://addyosmani.com/backbone-fundamentals/#backbone-basics) i boken Developing BackboneJS Applications.*

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
  <input class="toggle" type="checkbox" {%raw%}{{#if completed}}{%endraw%} checked {%raw%}{{/if}}{%endraw%}>
  <label>{%raw%}{{title}}{%endraw%}</label>
  <button class="destroy"></button>
</div>
<input class="edit" value="{%raw%}{{title}}{%endraw%}">
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

##### En notice om navigering och state

Det är också värt att notera att i klassisk webbutveckling krävde navigering mellan oberoende visningar användningen av en siduppdatering. I en single-page JavaScript-applikationer, hämtas data från en server via Ajax som kan dynamiskt återges i en ny vy på samma sida. Eftersom detta inte uppdaterar webbadressen automatiskt, faller rollen för navigering sålunda till en "router", som hjälper till att hantera applikationstillstånd (t.ex. tillåter användare att bokmärke en viss vy de har navigerat till).


#### Controllers

I vår Todo-applikation är en Controller ansvarig för hanteringen av ändringar som användaren gjort i edit View för en viss Todo och uppdatering av en specifik Todo-modell när en användare har slutat redigera.

Det är Controllers som de flesta JavaScript MVC-ramverk avviker från den traditionella tolkningen av MVC-mönstret. Anledningen till detta varierar, men enligt min mening såg JavaScript-ramverk författare initialt på server-side MVC (som Ruby on Rails) och insåg att tillvägagångssättet inte översatte 1:1 på klientsidan, och återtolkade C i MVC för att lösa sitt problem med state förvaltningen. Detta var ett smart tillvägagångssätt, men det kan göra det svårt för utvecklare att komma till MVC för första gången för att förstå både det klassiska MVC-mönstret och den "korrekta" rollen som Controllers har i andra JavaScript-ramverk.

Så har Backbone.js Controllers? Inte riktigt. Backbone innehåller vanligtvis "Controller" -logik, och routrar används för att hantera applikationstillstånd, men är inte en sann Controllers logig enligt klassisk MVC. I det avseendet, i motsats till vad som kan nämnas i den officiella dokumentationen eller i blogginlägg, är Backbone inte ett riktigt MVC-bibliotek. Det är faktiskt bättre att se den vara medlem i MV* -familjen som riktar in sig på sin egen arkitekturella väg. 

## Summering

Sammanfattningsvis hjälper MVC-mönstret dig att behålla din applikationslogik separat från ditt användargränssnitt, vilket gör det lättare att ändra och underhålla båda. Tack vare denna logikavskiljning är det tydligare varför förändringar i dina data, gränssnitt eller affärslogik måste göras och för vad dina enhetstester ska skrivas.

Efter att ha granskat det [klassiska MVC-mönstret](/2017/mvc-model-view-controller/) bör du nu förstå hur det gör det möjligt för utvecklare att separat separera problem i en applikation. Du bör också uppskatta hur JavaScript MVC-ramverk kan skilja sig åt i tolkningen av MVC ([Todo-applikation](/2017/backbonejs-client-side-mvc-style/)), och hur de delar några av de grundläggande begreppen i det ursprungliga mönstret.

När du granskar ett nytt JavaScript MVC/MV* -ramverk, kom ihåg - det kan vara användbart att gå tillbaka och överväga hur det valts att närma sig Modeller, Vyer, Controllers eller andra alternativ, eftersom det här bättre kan hjälpa dig att förstå hur ramverket ska utnytjas.

## Backbone.js Snabba fakta

* Kärnkomponenter: Modell, Vyer, Kollektioner, Router. Förfarer sin egen smak av MV*
* Event-driven kommunikation mellan Vyer och Modeller. Som vi ser är det relativt simpelt att lägga till händelselyssnare till något attribut i en modell, vilket ger utvecklare finkornig kontroll över ändringar i Vyn
* Stödjer databindningar genom manuella händelser eller ett separat Key-value observing (KVO) bibliotek.
* Stöd för RESTful gränssnitt, så Modeller enkelt kopplas till en backend
* Omfattande händelsessystem. Det är trivialt att lägga till stöd för pub/sub i Backbone
* Prototyper är instansierade med det new nyckelordet, vilket vissa utvecklare föredrar
* Agnostic om templerande ramverk, men Underscores mikro-templering är som standard tillgängliga
* Tydliga och flexibla konventioner för strukturering av applikationer. Backbone tvingar inte användningen av alla dess komponenter och kan fungera med bara de som behövs


