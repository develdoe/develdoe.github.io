---
date: '2018-02-26 16:30 +0100'
published: true
title: VeeValidate
---

## Vad är VeeValidate

Det här är ett plugin för Vue.js som låter dig validera inmatningsfält och visa fel på ett enkelt och kraftfullt sätt.

Du behöver inte göra något spacifikt i din app. Det mesta av arbetet ärt ren html. Du behöver bara ange, för varje input, vilken typ av validatorer som ska användas när värdet ändras. Du får då information om felen för varje fält.

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
