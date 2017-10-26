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
// Definiera en Todo-modell
var Todo = Backbone.Model.extend({
    // default todo attribute values
    defaults: {
        title: '',
        completed: false
    }
})

// Instansierar Todo-modellen med en `title`, med det `completed' attributet
// default till false
var myTodo = new Todo({
    title: 'Checkout attributes of the logged models in the console.'
})
```

Vår Todo Model utökar Backbone.Model och definierar helt enkelt standardvärden för två dataattribut. Som du kommer att upptäcka i de kommande kapitlen, ger Backbone-modellerna många fler funktioner, men den här enkla modellen illustrerar att först och främst en modell är en datakontainer.

Varje Todo-instans kommer att göras på sidan av en TodoView:

```js
var TodoView = Backbone.View.extend({

    tagName:  'li',

    // Cache mallfunktionen för ett enda objekt.
    todoTpl: _.template( $('#item-template').html() ),

    events: {
        'dblclick label': 'edit',
        'keypress .edit': 'updateOnEnter',
        'blur .edit': 'close'
    },

    // Kallas när visningen skapas först
    initialize: function() {
        this.$el = $('#todo')
        // Later we'll look at:
        // this.listenTo(someCollection, 'all', this.render);
        // but you can actually run this example right now by
        // calling todoView.render();
    },

    render: function() {
        this.$el.html( this.todoTpl( this.model.attributes ) );
        // $el här är en referens till jQuery-elementet
        // associerad med visningen är todoTpl en referens
        // till en Underscore mall och model.attributes
        // som innehåller attributen till modellen.
        // Sammanfattningsvis ersätter uttalandet HTML för
        // ett DOM-element med resultatet att instansera a
        // mall med modellens attribut.
        this.input = this.$('.edit');
        return this;
    },

    edit: function() {
        // körs när todo-etiketten är dubbelklickad
    },

    close: function() {
        // körs när todo förlorar fokus
    },

    updateOnEnter: function( e ) {
        // exekveras på varje knapptryckning när du gör det i redigeringsläget,
        // men vi väntar på att komma in för att komma igång
    }
})

// skapa en vy för en todo
var todoView = new TodoView({model: myTodo})
```

TodoView definieras genom att förlänga Backbone.View och är instansierad med en tillhörande modell.

I vårt exempel använder metoden `render()` en mall för att konstruera HTML för Todo-objektet som placeras inuti ett li-element. Varje anrop till `render()` kommer att ersätta innehållet av li-elementet med den aktuella modelldatan. Således gör en View-instans innehållet i ett DOM-element med attributen till en tillhörande modell. Senare ser vi hur en vy kan binda sin `render()` metod till modelländringshändelser, vilket gör att visningen återställs när modellen ändras.

Hittills har vi sett Backbone.Model implementerar modellaspekten av MVC och Backbone.View implementerar View. Men som vi noterade tidigare, avviker Backbone från traditionell MVC när det gäller Controllers - det finns ingen Backbone.Controller!

I stället adresseras Controller ansvar inom en Vy. Kom ihåg att Controller svarar på förfrågningar och utför lämpliga åtgärder som kan leda till förändringar i modellen och uppdateringar till vyn. I en applikation på en sida, istället för att ha förfrågningar i traditionell mening, har vi händelser. Händelser kan vara traditionella DOM-händelser för webbläsare (t ex klick) eller interna programhändelser som modelländringar.

I vår TodoView uppfyller händelseattributet rollen som Controller konfigurator, och definierar hur händelser som förekommer i View's DOM-element ska dirigeras till händelsehanteringsmetoder som definieras i Vyn.

Medan händelser i detta fall hjälper oss att relatera Backbone till MVC-mönstret, ser vi dem spela en mycket större roll i våra SPA-applikationer. Backbone.Event är en grundläggande ryggradskomponent som blandas i både Backbone.Model och Backbone.View, vilket ger dem rika eventhanteringsfunktioner. Observera att den traditionella kontrollerns roll (Smalltalk-80-stil) utförs av mallen, inte av Backbone.View.


