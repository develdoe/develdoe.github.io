---
date: '2018-04-26 19:37 +0200'
published: true
title: 'Flexbox, koncept'
---
## A New Post

Flexiboksmodulen, vanligtvis kallad flexbox, utformades som en endimensionell layoutmodell och som en metod som kan erbjuda rymdfördelning mellan objekt i ett gränssnitt och kraftfulla anpassningsfunktioner. 

Denna artikel ger en översikt över gundkoncepten med flexbox.

> När vi beskriver flexbox som endimensionell beskriver vi det faktum att flexbox hanterar layout i en dimension i taget - antingen som en rad eller som en kolumn. Detta  i motsättning mot den tvådimensionella modellen av CSS Grid, som styr kolumner och rader tillsammans.

### De två axlarna av flexbox

När du arbetar med flexbox måste du tänka på två axlar - huvudaxeln och korsaxeln. Huvudaxeln definieras av flexriktningsegenskapen och korsaxeln löper vinkelrätt mot den. Allt vi gör med flexbox hänvisar till dessa axlar, så det är värt att förstå hur de fungerar från början.

#### Huvudaxeln

Huvudaxeln definieras av flexriktning, som har fyra möjliga värden:

* rad
* rad-omvänd
* kolumn 
* kolumn-omvänd

Om du väljer rad eller rad-omvänd, kommer din huvudaxel att springa längs raden i inriktningsriktningen.

![](https://mdn.mozillademos.org/files/15614/Basics1.png)

Välj kolumn eller kolumn-omvänd och din huvudaxel kommer att springa från toppen av sidan till botten - i blockriktningen.

![](https://mdn.mozillademos.org/files/15615/Basics2.png)

#### Korsaxeln

Korsaxeln löper vinkelrätt mot huvudaxeln, därför om din flexriktning (huvudaxel) är inställd på rad eller rad-omvänd kör korsets axel nedåt i kolumnerna.

![](https://mdn.mozillademos.org/files/15616/Basics3.png)

Om din huvudaxel är kolumn eller kolumn-omvänd, går korsaxeln längs raderna.

![](https://mdn.mozillademos.org/files/15617/Basics4.png)


Att förstå vilken axel som är vilken är viktigt när vi börjar titta på `aligning` och `justifying` flexobjekt; flexbox har egenskaper som `align`
och `justify` innehåll längs en axel eller den andra.

---

### Start och slutet linjer

Ett annat viktigt område att förstå är hur flexbox inte antar skrivläget för dokumentet. Tidigare var CSS tungt vägd mot horisontella och vänster-till-höger skrivlägen. Moderna layoutmetoder omfattar fler skrivlägen så vu antar  inte längre att en rad text börjar längst upp till vänster i ett dokument och kör mot höger sida med nya linjer som visas under varandra.

Du kan läsa mer om förhållandet mellan flexbox och specifikationen Writing Modes senare i artikel, men följande beskrivning ska hjälpa till att förklara varför vi inte pratar om vänster och höger, upp och ner när vi beskriver den riktning som våra flexposter strömmar i.

Om flex-riktningen är rad och jag jobbar med engelska, så kommer startkanten på huvudaxeln till vänster, ändkanten till höger.

![](https://mdn.mozillademos.org/files/15618/Basics5.png)

Om jag skulle arbeta på arabiska skulle startkanten på min huvudaxel vara till höger och ändkanten till vänster.

![](https://mdn.mozillademos.org/files/15619/Basics6.png)

I båda fallen ligger kors axelns början på toppen av flexbehållaren och ändkanten i botten, eftersom båda språk har ett horisontellt skrivläge.

Efter ett tag blir tanken om start och slut i stället för vänster och höger naturligt, och det kommer att vara användbart för dig när du arbetar med andra layoutmetoder som CSS Grid Layout som följer samma mönster.

---

### Flexbehållaren

Ett område av ett dokument som läggs ut med flexbox kallas en flexcontainer. 
För att skapa en flexcontainer ställer vi in värdet av områdets containers displayegenskap till flex eller inline-flex. Så snart vi gör det blir de direkta barnen i den containern, flexiga saker. Som med alla egenskaper i CSS definieras några initialvärden, så när man skapar en flexbehållare uppträder alla inbyggda flexobjekt på följande sätt:

* Objekten visas i en rad (flex-riktningen initialvärde är rad).
* Barn föremålen startar från huvudaxelns startkant.
* Föremålen sträcker sig inte på huvuddimensionen, men kan krympa.
* Föremålen sträcker sig för att fylla storleken på korsaxeln.
* Flex-basegenskapen är inställd på auto.
* Flex-wrap-egenskapen är inställd på nowrap.

Resultatet av detta är att dina barn föremål kommer alla att rada i rad, med storleken på innehållet som deras storlek i huvudaxeln. Om det finns fler föremål än vad som passar in i behållaren, kommer de inte att slingras men istället överflöda. Om vissa föremål är längre än andra sträcker sig alla objekt längs korsaxeln för att fylla sin fulla storlek.

Du kan se i exemplet nedan hur det ser ut.

![]({{site.baseurl}}/img/posts/2018-04-27 08_10_37-Basic concepts of flexbox - CSS_ Cascading Style Sheets _ MDN.png)

