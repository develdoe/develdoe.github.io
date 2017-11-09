---
date: '2017-11-02 07:34 +0100'
published: true
title: JavaScript - this
---
*Denna artikel är en förkortning och försök till förenkling av orginal artikeln [Understand JavaScript’s “this” With Clarity, and Master It](http://javascriptissexy.com/understand-javascripts-this-with-clarity-and-master-it/)*

`this` nykelordet i JavaScript förvirrar nya och erfarna JavaScript-utvecklare. Denna artikel syftar till att belysa detta i sin helhet. Vi kommer att förstå hur man använder `this` korrekt i alla scenarier, inklusive de tjusiga situationer där det vanligtvis visar sig vara mest oroväckande.

Vi använder `this` här på samma sätt som vi använder pronomen på naturliga språk som engelska och franska. Vi skriver, "John löper fort för att "han" försöker fånga tåget."

Notera användningen av pronomen "han". Vi kunde ha skrivit det här: "John springer snabbt, eftersom John försöker fånga tåget." På ett liknande graciöst sätt, i JavaScript använder vi `this` nyckelordet som en genväg, en referent; det hänvisar till ett föremål; det vill säga ämnet i sammanhang eller föremålet för exekveringskoden. Tänk på detta exempel:

```js
var person = {
    firstName: "Penelope",
    lastName: "Barrymore",
    fullName: function() { // observera att vi använder "this" precis som vi använde "han" i tidigare
        console.log(this.firstName + " " + this.lastName); // Vi kunde också ha skrivit this
        console.log(person.firstName + " " + person.lastName);
    }
}
```

Om vi använder person.firstName och person.lastName, som i det sista exemplet blir vår kod tvetydig.

Tänk på att det kan finnas en annan global variabel (som vi kanske eller kanske inte är medveten om) med namnet "person". Därefter kan referenser till person.firstName försöka komma åt egenskapen firstName från den globala variabelen, vilket kan leda till till svåra att felsöka fel.

Så vi använder `this` nyckelordet inte bara för estetik (det vill säga som referent), men också för precision; dess användning gör faktiskt vår kod mer entydig, 

Precis som pronomen "han" används för att hänvisa till antecedenten (antecedent är det substantiv som en pronomen refererar till), används `this` nyckelordet också på ett objekt som funktionen (där den här används) är bunden till.

## JavaScript this nyckelordets grunder

Först, vet att alla funktioner i JavaScript har egenskaper, precis som objekt har egenskaper.

Och när en funktion körs får den `this` egenskapen - en variabel med **värdet på objektet som anropade funktionen där `this` används**.

`this` referensen hänvisar ALLTID till (och håller värdet av) ett objekt, ett singulärt objekt, och det brukar användas inom en funktion eller metod, även om det kan användas utanför en funktion i det globala scopet.

**Observera att när vi använder strikt läge, `this` håller värdet av 'undefined' i globala funktioner och i anonyma funktioner som inte är bundna till något objekt.**

`this` används inom en funktion (låt oss säga funktion A) och den innehåller värdet på objektet som åberopade funktion A. Vi behöver 'this' för att komma åt metoder och egenskaper på objektet som åberopade funktion A, särskilt eftersom vi inte alltid vet namnet på det uppkallande objektet, och ibland finns inget namn för att hänvisa till det åberopade objektet.

`this` är egentligen bara en genvägsreferens för "föregångsobjektet" - det uppkallande objektet.

Grubbla på detta grundläggande exempel som illustrerar användningen av `this` i JavaScript:

```js
var person = {
    firstName: 'Andree',
    lastName: 'Ray',
    //Eftersom 'this' sökordet används i metoden ShowFullName nedan och metoden
    // ShowFullName definieras på personobjektet,  'this' kommer att ha värdet
    // på personobjektet eftersom personobjektet anropar showFullName()
    showFullName: function () {
        console.log (this.firstName + " " + this.lastName);
    }
}

person.showFullName(); // => Andree Ray
```

Och överväga det här grundläggande jQuery-exemplet:

```js
$("button").click(function(event) {
    // $(this) kommer att ha värdet på knappen ($("button")) objektet.
    // eftersom knappobjektet anropar metoden click()
    console.log($(this).prop("name"));
});
```

Jag ska redogöra för det föregående jQuery-exemplet: Användandet av `$(this)`, vilket är jQuerys syntax för `this` nyckelord i JavaScript, används inom en anonym funktion, och den anonyma funktionen exekveras i knappens click() metod. Anledningen till att `$(this)` är knutet till knappobjektet beror på att jQuery-biblioteket binder `$(this)` till objektet som anropar klickmetoden.

