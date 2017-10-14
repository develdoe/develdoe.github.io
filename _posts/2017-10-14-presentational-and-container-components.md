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
* Exempel: Page, PagePanel, Story, UserInfo, List.

#### React container components:

* Är bekymrad över hur sakerna fungerar.
* Kan innehålla både presentations och container komponenter inuti men brukar inte ha någon DOM-uppsättning förutom vissa inslagspartier, och har aldrig några stilar.
* Erbjuder data och beteende till presentations eller andra container komponenter.
* Anroper Redux actions och tillhandahålle dem som återkopplingar till presentationsdelarna.
* Är ofta stateful, eftersom de tenderar att fungera som datakällor.
* Genereras vanligen med hjälp av högre orderkomponenter som `connect()` från React Redux, `createContainer()` från Relay eller `Container.create()` från Flux Utils, istället för att skrivas för hand.
* Exempel: UserPage, FollowersSidebar, StoryContainer, FollowedUserList.

Du kan lägga dem i olika mappar för att göra denna åtskillnad tydlig.

#### Benefits

* Bättre separation av ärende (eng: concerns). Du förstår din app och ditt användargränssnitt bättre genom att skriva komponenter på så sätt.
* Bättre återanvändning. Du kan använda samma presentationsdel med helt olika state-skällor och omvandla dem i separata behållarkomponenter som kan återanvändas.
* Presentationskomponenter är huvudsakligen din apps "palett". Du kan lägga dem på en enda sida och låta konstruktören anpassa alla sina variationer utan att röra appens logik. Du kan köra skärmdumps regressionstest på den sidan.
* Detta tvingar dig att extrahera "layoutkomponenter", som Sidebar, Page, ContextMenu och använd this.props.children istället för att duplicera samma markup och layout i flera behållarkomponenter.
* Kom ihåg att komponenter inte behöver avge DOM. De behöver bara tillhandahålla kompositionsgränser mellan UI ärenden (eng: concerns).
