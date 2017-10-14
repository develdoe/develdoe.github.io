---
date: '2017-10-14 17:55 +0200'
published: true
title: Stateless functional components
---
I idiomatisk React-kod kommer de flesta av de komponenter du skriver att vara statslösa, helt enkelt komponera andra komponenter.

React introducerar en ny, enklare syntax för dessa komponenter där du kan ta rekvisita som ett argument och returnera det element du vill:

```js
// A functional component using an ES2015 (ES6) arrow function:
var Aquarium = (props) => {
  var fish = getFish(props.species);
  return <Tank>{fish}</Tank>;
};

// Or with destructuring and an implicit return, simply:
var Aquarium = ({species}) => (
  <Tank>
    {getFish(species)}
  </Tank>
);

// Then use: <Aquarium species="rainbowfish" />
```
Dessa komponenter uppför sig precis som en React-klass med endast en definierad renderingsmetod.

Eftersom ingen komponent instans skapas för en funktionell komponent, kommer någon ref till en att utvärdera till null.

Funktionella komponenter har inte livscykelmetoder, men du kan ställa in .propTypes och .defaultProps som egenskaper på funktionen.

Detta mönster är utformat för att uppmuntra skapandet av dessa enkla komponenter som bör innehålla stora delar av dina appar.

*I framtiden kan vi också göra prestationsoptimeringar som är specifika för dessa komponenter genom att undvika onödiga kontroller och minnesallokeringar.*