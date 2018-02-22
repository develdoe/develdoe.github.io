---
date: '2018-02-22 14:20 +0100'
published: true
title: Skapa anpassade Vue.js-plugins
---
Vue.js-plugins är ett kraftfullt men enkelt sätt att lägga till globala features i din app. De har en mängd olika användningsområden, från att distribuera appövergripande komponenter för att lägga till ytterligare funktioner som routing och immutable datalager till din app.

Konceptuellt är ett Vue-plugin otroligt enkelt. Det är egentligen bara ett objekt med en install metod som tar två parametrar: Det globala Vue-objektet och ett objekt som innehåller användardefinierade alternativ. Ett pluginsystem som är så enkelt som detta kan dock fortfarande användas till stor effekt.

## Din första plugin

Som en introduktion till Vue-pluginsvärlden kommer vi att skriva ett grundläggande plugin som skriver till konsolen varje gång en komponent är monterad på DOM.


```js
// Detta är ditt plugin objekt. det kan exporteras för att användas vartsom
const Plugin = {
    // install metoden är allt som behöver existera på plugin-objektet.
    // Det tar det globala Vue-objektet såväl som användardefinierade alternativ.
    install(Vue, options) {
        // Vi kallar Vue.mixin () här för att injicera funktionalitet i alla komponenter.
        Vue.mixin({
            // Allt som läggs till en mixin injiceras i alla komponenter.
            // I det här fallet körs mounted() metoden när komponenten läggs till DOM.
            mounted() {
                console.log('Mounted!')
            }
        })
    }
}
export default Plugin
```

Din plugin kan nu användas i en Vue-app genom att importera den och anropa Vue.use(Plugin).

```js
import Vue from 'vue'
import MyPlugin from './my-vue-plugin.js'
import App from './App.vue'

// Pluginen laddas här
Vue.use(MyPlugin)

new Vue({
  el: '#app',
  render: h => h(App)
})
```

Du kanske undrar, varför kunde jag inte bara göra det genom att anropa Vue.mixin() i main.js? Anledningen är att eftersom vi lägger till global funktionalitet till Vue som inte ändrar appen direkt, är det nästan alltid bäst att dela upp det i en separat modul som kan läggas till eller tas bort efter vilja. Det gör också plugins otroligt lätta att distribuera.

## Lägga till ytterligare funktionallitet

### Installera App-Wide Components & Directives

Om du vill paketera komponenter eller direktiv som ska distribueras som ett plugin kan du skriva något så här:

```js
import MyComponent from './components/MyComponent.vue'
import MyDirecitve from './direcitves/MyDirectives.js'

const ComAndDirPlugin {
    install(Vue, options) {
        Vue.component(MyComponent.name, MyComponent)
        Vue.directive(MyDirective.name, MyDirective)
    }
}
export default ComAndDirPlugin
```

### Ändra det globala Vue-objektet

Du kan ändra det globala Vue-objektet från ett plugin så mycket som man kan förvänta sig:

```js
const MyPlugin {
  install(Vue, options) {
    Vue.myAddedProperty = 'Example Property'
    Vue.myAddedMethod = function() {
   	  return Vue.myAddedProperty
    }
  }
};

export default MyPlugin;
```

### Modifiera Vue Instancer

För att lägga till en egenskap eller metod direkt till komponentinstanser utan någon injektionsmekanism kan du ändra Vue prototypen som visas här:

```js
const MyPlugin {
  install(Vue, options) {
    Vue.prototype.$myAddedProperty = 'Example Property'
    Vue.prototype.$myAddedMethod = function() {
   	  return Vue.myAddedProperty
    }
  }
};

export default MyPlugin;
```

Dessa egenskaper kommer nu att läggas till alla komponenter och Vue-instanser.

**Within the Vue community, it is generally expected that plugins which modify the Vue prototype prefix any added properties with a dollar sign ($).**

### Lägga till komponent livscykelhakar eller egenskaper

Som visas ovan i exemplet "Din första plugin" kan du lägga till livscykelhakar och göra andra modifieringar av Vue-komponenter med hjälp av en Mixin.

```js
const Plugin = {
    install(Vue, options) {
        Vue.mixin({
            mounted() {
                console.log('Mounted!')
            }
        })
    }
}
export default Plugin
```

