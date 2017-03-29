---
date: '2017-03-29 01:45 +0200'
published: true
title: JavaScript - Grundhandboken Introduktion
category:
  - JavaScript
---
> I det här kapitlet av JavaScript diskuterar vi några av dess grundläggande begrepp.

## Vad du redan bör veta

Denna handbok förutsätter att du har följande grundläggande bakgrund:

* En allmän förståelse för Internet och webben (WWW).
* Goda kunskaper i Hypertext Markup Language (HTML).
* Viss erfarenhet av programmering. Om du är ny på programmering, prova en av de guider länkade på huvudsidan om [JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript).

## Vart du hittar information om JavaScript

MDN är en utmärkt platts att hitta information kring JavaScript. Jag hjälper till att översätta på nätverket och denna artikel hittar du även där. 

JavaScript dokumentation på MDN omfattar följande:

* [Learning the Web](https://developer.mozilla.org/en-US/docs/Learn) ger information för nybörjare och introducerar grundläggande begrepp inom programmering och Internet.
* [JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide) (Den här handboken) ger en översikt om JavaScript och dess objekt.
* [JavaScript Reference](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference) tillhandahåller detaljerad referensmaterial för JavaScript.

Om du är ny på JavaScript börja med artiklarna i [learning area](https://developer.mozilla.org/en-US/docs/Learn) och [JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide). När du har ett fast grepp om grunderna, kan du använda JavaScript Reference för att få mer information om enskilda objekt och uttryck.

## Vad är JavaScript?

JavaScript är ett plattformsoberoende, objektorienterat skriptspråk. Det är ett litet lättvikt språk som används i en värdmiljön (till exempel en webbläsare), JavaScript kan förbinda sig till värdens miljöobjekt, för att ge programmatisk kontroll över dem.

JavaScript innehåller ett standardbibliotek av objekt, såsom Array, Date, och Math, och en grundläggande uppsättning av språkelement såsom operatörer, styrstrukturer och uttryck. Kärnan i JavaScript kan förlängas för en mängd olika syften genom att komplettera den med ytterligare objekt, till exempel:

* *JavaScript på klientsidan* förlänger kärnspråket genom att tillägga objekt för att styra en webbläsare och dess Document Object Model (DOM). Till exempel, klientsidans förlägning tillåter en applikation placera elementen i ett HTML-formulär och svara på användarhändelser, såsom musklick, formulär input och sid navigering.
* *Server-side JavaScript* förlänger huvudspråket genom att leverera objekt som är relevanta för att köra JavaScript på en server. Till exempel, på serversidan tillåter utökningen en applikation kommunicera med en databas, ge kontinuitet av information från ett anrop till en annan applikation, eller utföra fil manipulationer på en server.

## JavaScript och Java

JavaScript och Java är liknande på vissa sätt, men fundamentalt annorlunda i vissa andra. JavaScript liknar Java men har inte Java's statiska typning och starka typkontroll. JavaScript har dock för de mesta samma syntax som Java,  som var anledningen till att man döpte om JavaScript från LiveScript.

Till skillnad från Javas kompilering av system klasser byggda av deklarationer, stöder JavaScript ett runtime system, baserat på ett litet antal datatyper som representerar numeriska, Boolean och strängvärden . JavaScript har en prototyp baserad objektmodell istället för den mer vanliga klassbaserade modell. Prototyp bas modellen ger dynamiskt arv; det vill säga vad som ärvs kan variera för enskilda objekt. JavaScript stöder också funktioner utan några speciella deklarativa krav. Funktioner kan vara egenskaper som tillhör objekt och exekveras såsom löst skrivna metoder.

JavaScript är ett mycket fritt formspråk jämfört med Java. Du behöver inte deklarera alla variabler, klasser och metoder. Du behöver inte bekymra sig om metoder är offentliga, privata eller skyddade och du behöver inte genomföra gränssnitt. Variabler, parametrar och funktionsreturtyper inte uttryckligen typad.

Java är ett programmeringsspråk klassbaserad avsedd för snabb exekvering och typsäkerhet. Typsäkerhet betyder, till exempel, att du inte kan kasta ett Java heltal till en objektreferens eller tillgå privat minne genom att korrumpera Java bytekoder . Javas klassbaseradmodell innebär att program består uteslutande av klasser och deras metoder. Java klass arv och stark typning kräver iallmänhet hårt kopplade objekt hierarkier. Dessa krav gör Java-programmering mer komplex än JavaScript programmering.

I kontrast, JavaScript stiger ned i ande från en linje av mindre, dynamiskt typade språk såsom Hypertalk och dBASE. Dessa skriptspråk ger programmeringsverktyg till en mycket bredare publik på grund av deras lättare syntax, specialiserade inbyggd funktionalitet och minimikrav för att skapa objekt.

**JavaScript jämfört med Java**

|JavaScript|Java|
|---|---|
|Objektorienterad. Ingen åtskillnad mellan olika typer av objekt. Arv är genom prototypen mekanismen, och egenskaper och metoder kan läggas till alla objekt dynamiskt.|Klass-baserade. Objekt är indelade i klasser och instanser med all arv genom klasshierarkin . Klasser och instanser kan inte ha egenskaper eller metoder tillsatta dynamiskt.|
|Variablers datatyper är inte deklarerade (dynamisk typning).|Variablel datatyper måste deklareras (statisk typning).|
|Det går inte att automatiskt skriva till hårddisken.|Kan automatiskt skriva till hårddisken.|

För mer information om skillnaderna mellan JavaScript och Java, se kapitlet [Details of the object model](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Details_of_the_Object_Model).

## JavaScript och ECMAScript specifikationen

JavaScript är standardiserad vid [Ecma International](http://www.ecma-international.org/) — den europeiska sammanslutningen för standardisering informations- och kommunikationssystem (ECMA var tidigare en akronym för Europeiska Computer Manufacturers Association) för att leverera en standardiserad internationell programmeringsspråk baserad på JavaScript. Denna standardiserade versionen av JavaScript, kallad ECMAScript, beter sig på samma sätt i alla program som stöder standarden. Företag kan använda den öppna standarden av språket för att utveckla en egen implementation av JavaScript. ECMAScript standard dokumenteras i ECMA-262-specifikationen. Se [New in JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/New_in_JavaScript) för att lära dig mer om olika versioner av JavaScript och ECMAScript specifikations upplagor

ECMA-262 standarden är också godkänd av [ISO](https://www.iso.org/home.html) (International Organization for Standardization) som ISO-16262. Du kan också hitta specifikation på [Ecma International](http://www.ecma-international.org/publications/standards/Ecma-262.htm)'s hemsida. ECMAScript specifikationen beskriver inte Document Object Model (DOM), som standardiserats av [Worls Wide Web Consortium (W3C)](https://www.w3.org/) och/eller [WHATWG (Web Hypertext Application Technology Working Group)](https://whatwg.org/). DOM definierar det sätt på vilket HTML dokument objekt exponeras för ditt skript. För att få en bättre uppfattning om de olika tekniker som används vid programmering med JavaScript, konsultera artikeln [JavaScript technologies overview](https://developer.mozilla.org/en-US/docs/Web/JavaScript/JavaScript_technologies_overview).

### JavaScript dokumentation kontra ECMAScript specifikationen

ECMAScript specifikationen är en uppsättning regler för att implementera ECMAScript; vilket är användbart om du vill genomföra standard kompatibla språkfunktioner av ECMAScript implementation eller motor (t.ex. Spidermonkey i Firefox eller v8 i Chrome).

ECMAScript specifikationen är inte avsett att hjälpa skript programmerare; använda JavaScript dokumentationen för information om hur du skriver skript.

ECMAScript specifikationen använder terminologi och syntax som kan vara främmande för en JavaScript programmerare. Även om beskrivningen av språket kan skilja sig i ECMAScript, förblir själva språket densamma . JavaScript stöder alla funktioner som beskrivs i ECMAScript specifikationen.

Däremot så finns det i JavaScript dokumentationen aspekter av språket som är lämpligt för en JavaScript programmerare.

## Komma igång med JavaScript

Komma igång med JavaScript är enkelt: allt du behöver är en modern webbläsare. Denna guide innehåller några JavaScript funktioner som endast tillgängliga i de senaste versionerna av Firefox, så använder den senaste versionen av Firefox rekommenderas.

Det finns två verktyg inbyggda i Firefox som är användbara för att experimentera med JavaScript: Webb Konsol och Kladdblocket.

### Webb Konsol

[Webb Konsol](https://developer.mozilla.org/en-US/docs/Tools/Web_Console) visar information om den aktuellat laddade webbsida och innehåller även en kommandorad som du kan använda för att köra JavaScript uttryck i den aktuella sidan.

För att öppna webbkonsolen (`Ctrl`+`Shift`+`K` på Windows och Linux, eller `Cmd`-`Option`-`K` på mac), välj "Webb Konsole" från "Webbutvecklare" menyn,som är under menyn "Verktyg" i Firefox. Det visas längst ned i webbläsarfönstret . Längs botten av konsolen är en kommandorad som du kan använda för att komma in JavaScript och utgången visas i rutan ovan:

![Webb Konsol](https://mdn.mozillademos.org/files/7363/web-console-commandline.png)

### Kladdblocket

Webb Konsol är bra för att utföra enkla rader JavaScript, men även om du kan utföra flera rader, är den inte bekvämt för det. Du kan heller inte spara dina kodexempel när du använder webbkonsolen. För mer komplexa exempel är  Kladdblocket ett bättre verktyg.

För att öppna Kladdblocket (`Shift`+`F4`), och välj "Kladdblocket" from the "Webbutvecklare" menyn, som är under menyn i Firefox. Den öppnas i ett separat fönster och är en editor som du kan använda för att skriva och exekvera JavaScript i webbläsaren. Du kan också läsa/spara in dem från disk.

![Kladdblocket](https://mdn.mozillademos.org/files/13468/scratchpad.png)

### Hello world

För att komma igång med att skriva JavaScript öppnar Kladdblocket och skriv din första "Hello world" JavaScript-kod:

```js
function greetMe(yourName) {
  alert('Hello ' + yourName);
}

greetMe('World');
```

Välj koden i blocket och tryck ner Ctrl+R för att se det utvecklas i din webbläsare!

På följande sidor kommer den här guiden introducera dig till JavaScript syntax och språkfunktioner, så att du kommer att kunna skriva mer komplexa applikationer.

