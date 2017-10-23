---
date: '2017-10-14 17:42 +0200'
published: true
title: React - Presentational and Container Components
---
Det finns ett enkelt mönster som jag tycker är oerhört användbart när du skriver React-applikationer.

Om du har gjort React ett tag har du förmodligen redan upptäckt det. Denna artikel förklarar det bra, men jag vill lägga till några fler poäng.

Dina komponenter lir mycket lättare att återanvända och motivera om du delar dem i två kategorier.

Jag kallar dem Container och Presentationskomponenter, men jag hörde också hört *Fet* and *Mager*, *Smart* and *Dumb*, *Stateful* och *Pure*, *Screens* coh  *Components*, etc.
s
These all are not exactly the same, but the core idea is similar.

#### React presentations komponenter:

* Berör över hur saker ser ut.
* Kan innehålla både presentations- och behållarkomponenter inuti, och brukar ha vissa DOM-markeringar och egna stilar.
* Ofta inneslutning via this.props.children.
* Har inga beroenden i resten av appen, som Redux actions eller store.
* Ange inte hur data laddas eller muteras.
* Ta emot data och skicakr callbacks exklusivt via props uteslutande via rekvisita.
* Sällan har sitt eget state (när de gör det, är det UI-state istället för data).
* Skrivs som [funktionella komponenter](http://developer.wimse.se/2017/stateless-functional-components/) om de inte behöver state, livscykelhakar eller prestandaoptimeringar.
* Exempel: Page, PagePanel, Story, UserInfo, List.

```js
// CommentList.js
import React from "react";

const Commentlist = comments => (
  <ul>
    {comments.map(({ body, author }) =>
      <li>{body}-{author}</li>
    )}
  </ul>
)
```

#### React container components:

* Är bekymrad över hur sakerna fungerar.
* Kan innehålla både presentations och container komponenter inuti men brukar inte ha någon DOM-uppsättning förutom vissa inslagspartier, och har aldrig några stilar.
* Erbjuder data och beteende till presentations eller andra container komponenter.
* Anroper Redux actions och tillhandahålle dem som återkopplingar till presentationsdelarna.
* Är ofta stateful, eftersom de tenderar att fungera som datakällor.
* Genereras vanligen med hjälp av högre orderkomponenter som `connect()` från React Redux, `createContainer()` från Relay eller `Container.create()` från Flux Utils, istället för att skrivas för hand.
* Exempel: UserPage, FollowersSidebar, StoryContainer, FollowedUserList.

```js
// CommentListContainer.js
import React from "react";
import CommentList from "./CommentList";

class CommentListContainer extends React.Component {
  constructor() {
    super();
    this.state = { comments: [] }
  }
  
  componentDidMount() {
    fetch("/my-comments.json")
      .then(res => res.json())
      .then(comments => this.setState({ comments }))
  }
  
  render() {
    return <CommentList comments={this.state.comments} />;
  }
}
```

Du kan lägga dem i olika mappar för att göra denna åtskillnad tydlig.

#### Benefits

* Bättre separation av ärende (eng: concerns). Du förstår din app och ditt användargränssnitt bättre genom att skriva komponenter på så sätt.
* Bättre återanvändning. Du kan använda samma presentationsdel med helt olika state-skällor och omvandla dem i separata behållarkomponenter som kan återanvändas.
* Presentationskomponenter är huvudsakligen din apps "palett". Du kan lägga dem på en enda sida och låta konstruktören anpassa alla sina variationer utan att röra appens logik. Du kan köra skärmdumps regressionstest på den sidan.
* Detta tvingar dig att extrahera "layoutkomponenter", som Sidebar, Page, ContextMenu och använd this.props.children istället för att duplicera samma markup och layout i flera behållarkomponenter.
* Kom ihåg att komponenter inte behöver avge DOM. De behöver bara tillhandahålla kompositionsgränser mellan UI ärenden (eng: concerns).

## When to Introduce Containers?

Jag föreslår att du först börjar bygga din app med bara presentativa komponenter först. Så småningom kommer du inse att du passerar för många props ner till närmaste komponenterna.

När du märker att vissa komponenter inte använder de rekvisita som de får men bara vidarebefordrar dem och du måste omdirigera alla de mellanliggande komponenterna när barnen behöver mer data, är det bra att introducera vissa behållarkomponenter.

På så sätt kan du få data och beteendestöd till nod-komponenterna utan att belasta de orelaterade komponenterna i mitten av trädet.

Detta är en pågående refactoringprocess, så försök inte få det rätt första gången.

När du experimenterar med detta mönster kommer du att utveckla en intuitiv känsla för när det är dags att extrahera några behållare, precis som du vet när det är dags att extrahera en funktion.

Den här gratis tutorialen [Redux Egghead](https://egghead.io/courses/getting-started-with-redux) serie är en bra vägledare.

## Other Dichotomies

Det är viktigt att du förstår att skillnaden mellan presentationsdelarna och behållarna inte är teknisk. Snarare är det en skillnad i deras syfte.

Däremot är det några relaterade (men olika!) Tekniska skillnader:

* **Stateful and Stateless** - Vissa komponenter använder React setState() metoden och vissa gör det inte. Medan behållarkomponenter tenderar att vara statefull och presenterande komponenter tenderar att vara statsless, är inte detta en hård regel. Presentationskomponenter kan vara statful, och behållare kan också vara statless.
* **Classes and Functions** - Eftersom Reakt 0,14 kan komponenter deklareras både som klasser och som funktioner. Funktionella komponenter är enklare att definiera men de saknar vissa funktioner som för närvarande endast är tillgängliga för klasskomponenter. Några av dessa begränsningar kan gå bort i framtiden men de finns idag. Eftersom funktionella komponenter är lättare att förstå, föreslår jag att du använder dem om du inte behöver state, livscykelhakar eller prestandaoptimeringar, som endast är tillgängliga för klasskomponenterna just nu.
* **Pure and Impure** - Man säger att en komponent är ren om den garanterat att returnera samma resultat given samma prop och state. Rena komponenter kan definieras både som klasser och funktioner, och kan vara både stateful och stateless. En annan viktig aspekt av rena komponenter är att de inte har några krav på djupa mutationer i props eller state, så deras prestanda kan optimeras genom en grundlig jämförelse i deras shouldComponentUpdate() -krok. För närvarande kan endast klasser definiera shouldComponentUpdate() men det kan ändras i framtiden.

Både presentationsdelar och behållare kan vara i någon av bäda dessa. Enligt min erfarenhet tenderar presentationsdelar att vara stateless rena funktioner, och behållare tenderar att vara stateful rena klasser. Men det här är inte en regel utan en observation, och jag har sett de exakta motsatta fallen som visste sig under vissa omständigheter.

Ta inte delningen av presentations- och behållarkomponenten som en dogma. Ibland spelar ingen roll eller det är svårt att rita linjen. Om du är osäker på om en viss komponent ska vara presentativ eller en behållare, kan det vara för tidigt att bestämma.

## Example

[This gist](https://gist.github.com/chantastic/fc9e3853464dffdb1e3c)

## Further Reading

[Getting Started with Redux](https://egghead.io/courses/getting-started-with-redux)

[Mixins are Dead, Long Live Composition](https://medium.com/@dan_abramov/mixins-are-dead-long-live-higher-order-components-94a0d2f9e750)

[Container Components](https://medium.com/@learnreact/container-components-c0e67432e005)

[Atomic Web Design](http://bradfrost.com/blog/post/atomic-web-design/)

[Building the Facebook News Feed with Relay](http://facebook.github.io/react/blog/2015/03/19/building-the-facebook-news-feed-with-relay.html)
