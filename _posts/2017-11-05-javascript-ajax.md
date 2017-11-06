---
date: '2017-11-05 19:00 +0100'
published: true
title: JavaScript - AJAX
---
*Denna artikel är en ren översättning och ett försök att förkorta ned original artikeln [Getting Started](https://developer.mozilla.org/en-US/docs/AJAX/Getting_Started) på MDN.*

Denna artikel guidar dig genom AJAX-grunderna och ger dig två enkla praktiska exempel för att komma igång.

## Vad är AJAX

AJAX står för asynkron JavaScript och XML. I ett nötskal är det användningen av XMLHttpRequest-objektet för att kommunicera med servrar. Den kan skicka och ta emot information i olika format, inklusive JSON, XML, HTML och textfiler. AJAX mest tilltalande egenskap är dess "asynkrona" natur, vilket innebär att den kan kommunicera med servern, byta data och uppdatera sidan utan att behöva uppdatera sidan.

De två huvuddragen i AJAX:

* Gör förfrågningar till servern utan att ladda om sidan
* Ta emot och arbeta med data från servern

## Steg 1 - Hur man gör en HTTP-begäran

För att kunna göra en HTTP-begäran till servern med JavaScript behöver du en förekomst av ett objekt med nödvändig funktionalitet. Det här är där XMLHttpRequest kommer in. Dess föregångare har sitt ursprung i Internet Explorer som ett ActiveX-objekt som heter XMLHTTP. Sedan följde Mozilla, Safari och andra webbläsare, genom att implementera ett XMLHttpRequest-objekt som stödde metoderna och egenskaperna hos Microsofts ursprungliga ActiveX-objekt. Under tiden implementerade Microsoft XMLHttpRequest också.

Efter en förfrågan får du ett svar tillbaka. Vid detta tillfälle måste du berätta för XMLHttp-förfrågan, vilken JavaScript-funktion som hanterar svaret, genom att ställa in objektets onreadystatechange-egenskap och namnge det efter att funktionen ska åberopas när förfrågan ändras, så här:

```js
httpRequest.onreadystatechange = nameOfTheFunction;
```

Observera att det inte finns några parenteser eller parametrar efter funktionsnamnet, eftersom du tilldelar en referens till funktionen istället för att faktiskt åberopa den. Alternativt kan du istället för att ge ett funktionsnamn använda JavaScript-tekniken för att definiera funktioner i på en gång (kallade "anonyma funktioner") för att definiera de åtgärder som ska hantera svaret, gör så här:

```js
httpRequest.onreadystatechange = function(){
    // Process the server response here.
};
```

Efter att ha förklarat vad som händer när du får svaret måste du faktiskt göra begäran genom att anropa metoderna för `open()` och `send()` för HTTP-förfrågan, så här:

```js
httpRequest.open('GET', 'http://www.example.org/some.file', true);
httpRequest.send();
```

* Den första parametern för anropet open() är HTTP-förfrågan - GET, POST, HEAD, eller en annan metod som stöds av din server. Håll metoden all-capitals enligt HTTP-standarden, annars kan vissa webbläsare (som Firefox) inte behandla förfrågan. För mer information om möjliga HTTP-förfrågningsmetoder, kolla [W3C-specifikationerna](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).

* Den andra parametern är den webbadress du skickar begäran till. Som säkerhetsfunktion kan du inte åberopa URL-adresser på tredje partens domäner som standard. Var noga med att använda det exakta domännamnet på alla dina sidor, eller så du kommer att få ett "behörighetsförnekat" fel när du anropar call(). Ett vanligt fel är att tillgå din webbplats via domain.tld, men istället örsöker anropar dem med www.domain.tld. Om du verkligen behöver skicka en begäran till en annan domän, se [HTTP access control](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).

* Den valfria tredje parametern anger om begäran är asynkron. Om true (standard), fortsätter JavaScript-körningen och användaren kan interagera med sidan medan serverns svar ännu inte har kommit fram. Detta är det första A i AJAX.

Parametern för metoden send() kan vara vilken data som helst som du vill skicka till servern om POST begäran. Formdata ska skickas i ett format som servern kan analysera, som en frågesträng:

```js
"name=value&anothername="+encodeURIComponent(myVar)+"&so=on"
```

eller andra format, som multipart/form-data, JSON, XML, och så vidare.

**Observera att om du vill använa `POST` data måste du kanske ställa in begärans MIME-typen. Till exempel, använd följande innan du anropar `send()` för formulärdata som skickas som en frågesträng:**

```js
httpRequest.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
```



