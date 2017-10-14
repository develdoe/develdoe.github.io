---
date: '2017-10-14 17:42 +0200'
published: true
title: Presentational and Container Components
---
Det finns ett enkelt mönster som jag tycker är oerhört användbart när du skriver React-applikationer.

Om du har gjort React ett tag har du förmodligen redan upptäckt det. Denna artikel förklarar det bra, men jag vill lägga till några fler poäng.

Du hittar dina komponenter mycket lättare att återanvända och motivera om du delar dem i två kategorier.

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
* Exempel: Sida, Sidpanel, Story, UserInfo, List.

#### React container components:

* Är bekymrad över hur sakerna fungerar.
* Kan innehålla både presentations och container komponenter inuti men brukar inte ha någon DOM-uppsättning förutom vissa inslagspartier, och har aldrig några stilar.
* Erbjuder data och beteende till presentations eller andra container komponenter.
* Anroper Redux actions och tillhandahålle dem som återkopplingar till presentationsdelarna.
* Är ofta stateful, eftersom de tenderar att fungera som datakällor.

