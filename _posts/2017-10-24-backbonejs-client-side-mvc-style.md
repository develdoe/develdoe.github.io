---
date: '2017-10-24 20:38 +0200'
published: true
title: BackboneJS - Client-Side MVC Style
---
Låt oss ta en titt på hur Backbone.js ger fördelarna med MVC till klient utveckling med hjälp av en Todo-applikation som vårt exempel. Vi kommer att bygga på det här exemplet i de kommande kapitlen när vi utforskar Backbone-funktioner men för närvarande fokuserar vi bara på kärnkomponenternas relationer till MVC.

Vårt exempel kommer att behöva ett div-element som vi kan bifoga en lista över Todos. Den kommer också att behöva en HTML-mall som innehåller en platshållare för en Todo-artikeltitel och en checkbox som kan instansieras för Todo-objektinstanser. Dessa tillhandahålls av följande HTML:

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title></title>
  <meta name="description" content="">
</head>

<body>
  <div id="todo"></div>

  <script type="text/template" id="item-template">
    <div class="view">
      <input id="todo_complete" type="checkbox" <%= completed ? 'checked="checked"' : '' %> />
      <label><%= title %></label>
      <button class="destroy"></button>
    </div>
    <input class="edit" value="<%= title %>">
  </script>

  <script src="jquery.js"></script>
  <script src="underscore.js"></script>
  <script src="backbone.js"></script>
  <script src="demo.js"></script>
```

I vår Todo-applikation (demo.js) används backbone-modellinstanser för att hålla data för varje Todo-artikel:

```js
// Define a Todo Model
var Todo = Backbone.Model.extend({
    // default todo attribute values
    defaults: {
        title: '',
        completed: false
    }
})

// Instatntiate the Todo MOdel with a title, with the completed attribute
// defaulting to false
var myTodo = new Todo({
    titile: 'Checkout attributes of the logged models in the console.'
})
```

Vår Todo Model utökar Backbone.Model och definierar helt enkelt standardvärden för två dataattribut. Som du kommer att upptäcka i de kommande kapitlen, ger Backbone-modellerna många fler funktioner, men den här enkla modellen illustrerar att först och främst en modell är en datakontainer.