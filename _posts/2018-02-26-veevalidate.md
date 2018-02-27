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

**html**
```html
<div class="columns is-multiline">
    <div class="column is-12">
        <label class="label">Email (1s delay)</label>
        <p class="control has-icon has-icon-right">
            <input name="email" v-validate="'required|email'" data-vv-delay="1000" :class="{'input': true, 'is-danger': errors.has('email') }" type="text" placeholder="Email">
            <i v-show="errors.has('email')" class="fa fa-warning"></i>
            <span v-show="errors.has('email')" class="help is-danger">{{ errors.first('email') }}</span>
        </p>
    </div>
    <div class="column is-12">
        <label class="label">Name (0.5s delay)</label>
        <p class="control has-icon has-icon-right">
            <input name="name" v-validate="'required|alpha'" data-vv-delay="500" :class="{'input': true, 'is-danger': errors.has('name') }" type="text" placeholder="Name">
            <i v-show="errors.has('name')" class="fa fa-warning"></i>
            <span v-show="errors.has('name')" class="help is-danger">{{ errors.first('name') }}</span>
        </p>
    </div>
</div>
```

## Försenad validering (debounced)

I ett scenario där du vill vänta på att användaren slutar skriva och sedan validera fältet. Detta kan uppnås genom att lägga till ett `data-vv-delay` attribut på fältet som valideras och tilldela det antal millisekunder du vill vänta på.

**html**
```html
<input name="email" v-validate="'required|email'" data-vv-delay="1000" :class="{'input': true, 'is-danger': errors.has('email') }" type="text" placeholder="Email">
```

## Models Validering

`v-validate` direktivet detekterar om input har v-model bunden till samma input, och tittar på det värdet och validerar det när det ändras. Men det kan också lyssna på förändringar i computed attribut genom att använda `arg` så här:

```html
<input v-validate:name="'required|alpha_spaces'" type="text" name="name">
```

```js
export default {
  data: () => ({
    first_name: '',
    last_name: ''
  }),
  computed: {
    name() {
      return this.first_name + ' ' + this.last_name;
    }
  }
};
```

**Uttrycket för både arg och  v-model direktivet måste vara ett enkelt pricknotationsuttryck, och uttrycket måste existera på förekomsten. Om du till exempel har inputs i en slinga som är bunden av iteratorn, fungerar det inte korrekt och används istället de traditionella lyssnare.**

Som du kan se är arg name som säger `v-validate` att titta på när namnet ändras.

**Du kan debounce validering med `lazy` modifierare på ditt v-model direktiv, vilket gör att valideringen endast ska ske när användaren lämnar inmatningen.**

## Initial Value Validation

I nästa exempel märker användningen av .initial modifier för att tvinga valideringen av fältets initialvärde.

```html
<input name="email" v-model="email" v-validate.initial="'required|email'" :class="{'input': true, 'is-danger': errors.has('email') }" type="text" placeholder="Email">
```

```js
export default {
  name: 'data-example',
  data: () => ({
    email: '',
    first_name: '',
    last_name: ''
  }),
  computed: {
    name() {
      return `${this.first_name} ${this.last_name}`;
    }
  }
};
```

## Validate Form Before Submit

Du kanske vill utlösa alla inmatningar validering innan du skickar in ett formulär, kanske en varning visas eller förhindra formulärinsändning om några fel upptäcks. Detta kan enkelt uppnås med `validateAll`  metod.

```html
<form @submit.prevent="validateBeforeSubmit">
    <div class="column is-12">
        <label class="label">Email</label>
        <p class="control has-icon has-icon-right">
            <input name="email" v-model="email" v-validate="'required|email'" :class="{'input': true, 'is-danger': errors.has('email') }" type="text" placeholder="Email">
            <i v-show="errors.has('email')" class="fa fa-warning"></i>
            <span v-show="errors.has('email')" class="help is-danger">{{ errors.first('email') }}</span>
        </p>
    </div>
    <div class="column is-12">
        <label class="label">Name</label>
        <p class="control has-icon has-icon-right">
            <input name="name" v-model="name" v-validate="'required|alpha'" :class="{'input': true, 'is-danger': errors.has('name') }" type="text" placeholder="Name">
            <i v-show="errors.has('name')" class="fa fa-warning"></i>
            <span v-show="errors.has('name')" class="help is-danger">{{ errors.first('name') }}</span>
        </p>
    </div>
    <div class="column is-12">
        <label class="label">Phone</label>
        <p class="control has-icon has-icon-right">
            <input name="phone" v-model="phone" v-validate="'required|numeric'" :class="{'input': true, 'is-danger': errors.has('phone') }" type="text" placeholder="Phone">
            <i v-show="errors.has('phone')" class="fa fa-warning"></i>
            <span v-show="errors.has('phone')" class="help is-danger">{{ errors.first('phone') }}</span>
        </p>
    </div>
    <div class="column is-12">
        <label class="label">Website</label>
        <p class="control has-icon has-icon-right">
            <input name="url" v-model="url" v-validate="'required|url'" :class="{'input': true, 'is-danger': errors.has('url') }" type="text" placeholder="Website">
            <i v-show="errors.has('url')" class="fa fa-warning"></i>
            <span v-show="errors.has('url')" class="help is-danger">{{ errors.first('url') }}</span>
        </p>
    </div>

    <div class="column is-12">
        <p class="control">
            <button class="button is-primary" type="submit">Submit</button>
        </p>
    </div>
</form>
```

```js
export default {
  name: 'form-example',
  data: () => ({
    email: '',
    name: '',
    phone: '',
    url: ''
  }),
  methods: {
    validateBeforeSubmit() {
      this.$validator.validateAll().then((result) => {
        if (result) {
          // eslint-disable-next-line
          alert('From Submitted!');
          return;
        }

        alert('Correct them errors!');
      });
    }
  }
};
```

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