---
date: '2018-02-26 16:30 +0100'
published: true
title: VeeValidate
---

## Vad är VeeValidate

Det här är ett plugin för Vue.js som låter dig validera inmatningsfält och visa fel på ett enkelt och kraftfullt sätt.

Du behöver inte göra något spacifikt i din app. Det mesta av arbetet är ren html. Du behöver bara ange, för varje input, vilken typ av validatorer som ska användas när värdet ändras. Du får då information om felen för varje fält.

Även om de flesta av valideringarna sker automatiskt så kan du använda valideraren så som passar dig. Valideringsobjektet har inga beroenden och är ett fristående objekt.

För närvarande finns över 20 valideringsregler tillgängliga i plugin.

## Komma igång

### Installation

Du kan installera det här pluginet via npm eller via en CDN.

**npm**
```bash
npm install vee-validate --save
```

**CDN**
```hmtl
 <!-- jsdelivr cdn -->
  <script src="https://cdn.jsdelivr.net/npm/vee-validate@latest/dist/vee-validate.js"></script>

  <!-- unpkg -->
  <script src="https://unpkg.com/vee-validate@2.0.0-rc.7"></script>
```

### Usage

```html
<script src="path/to/vue.js"></script>
<script src="path/to/vee-validate.js"></script>
<script>
  Vue.use(VeeValidate); // good to go.
</script>
```


eller så kan du importera med hjälp av ES6:

```js
import Vue from 'vue';
import VeeValidate from 'vee-validate';

Vue.use(VeeValidate);
```

## Grundläggande exempel


Allt du behöver är att lägga till `v-validate` direktivet till inmatningen du vill validera. Sedan vidarebefordra till direktivet en reglersträng som innehåller en lista över valideringsregler separerade av ett rör `|`.

Använd `required` för att ange att fältet är obligatoriskt och `email` för att ange att fältet måste vara ett e-postmeddelande.

För att kombinera båda reglerna tilldelar vi strängvärdet `required|email` till v-validate uttrycksvärdet.

## Tillgängliga regler

|:------------- |:-------------:| -----:|
|after|alpha|alpha|
|_dash|alpha_num|alpha_spaces|
|before|between|confirmed|
|credit_card|date_between|date_format|
|decimal|digits|dimensions|
|email|ext|image|
|in|ip|max|
|max_value|mimes|min|
|min_value|not_in|numeric|
|regex|required|size|
|url