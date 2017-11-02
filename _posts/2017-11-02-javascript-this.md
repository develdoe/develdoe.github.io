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

