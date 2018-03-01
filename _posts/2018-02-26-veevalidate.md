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

## Lokaliserade meddelanden

Du kanske vill visa felmeddelanden på olika språk, `vee-validate` erbjuder mycket flexibilitet för flerspråkiga, här är ett exempel på hur du kan göra det.

```html
<div class="columns is-rtl is-multiline">
    <button @click="changeLocale" type="button" class="button is-primary">Change Locale To {{ nextLocale }}</button>
    <div class="column is-12">
        <label class="label">البريد الاليكتروني (Email)</label>
        <p class="control has-icon has-icon-left">
            <input name="email" v-validate="'required|email'" :class="{'input': true, 'is-danger': errors.has('email') }" type="text" placeholder="Email">
            <i v-show="errors.has('email')" class="fa fa-warning"></i>
            <span v-show="errors.has('email')" class="help is-danger has-text-right">{{ errors.first('email') }}</span>
        </p>
    </div>
    <div class="column is-12">
        <label class="label">رقم الهاتف (Phone)</label>
        <p class="control has-icon has-icon-left">
            <input name="phone" v-validate="'required|numeric'" :class="{'input': true, 'is-danger': errors.has('phone') }" type="text" placeholder="Phone">
            <i v-show="errors.has('phone')" class="fa fa-warning"></i>
            <span v-show="errors.has('phone')" class="help is-danger has-text-right">{{ errors.first('phone') }}</span>
        </p>
    </div>
</div>
``` 

```js
import arabic from 'vee-validate/dist/locale/ar';

export default {
  name: 'locale-example',
  data: () => ({
    email: '',
    phone: '',
    locale: 'en',
  }),
  computed: {
    nextLocale() {
      return this.locale === 'en' ? 'Arabic' : 'English';
    }
  },
  methods: {
    changeLocale() {
      this.locale = this.$validator.locale === 'ar' ? 'en' : 'ar';
      this.$validator.localize(this.locale);
    }
  },
  created() {
    this.$validator.localize('ar', {
      messages: arabic.messages,
      attributes: {
        email: 'البريد الاليكتروني',
        phone: 'رقم الهاتف'
      }
    });

    // start with english locale.
    this.$validator.localize('en');
  }
};
```

## Scopes

Som standard är validatorns räckvidd den samma som den Vue-instans som äger den. Ibland kan du ha flera fält inom samma komponent, de är i olika former och tjänar olika syften. Valideraren behandlar sedan de två fälten som samma fält som kommer att orsaka problem att detektera inmatningen och visa felen.

Du kan berätta valideraren att omfatta fälten genom att lägga till en `data-vv-scope` attribut som ger valideraren namnet på omfattningen.

Dessa fält identifieras sedan med namnet och deras omfattning, du kan ha inmatningar med samma namn i olika scopes, och du kan visa, rensa och validera dessa områden oberoende.

För enkelhets skyld kan du lägga till `data-vv-scope` attribut på det formuläret, så behöver du inte lägga till attributet på varje input. Du kan också överföra `scope` attributet till valideringsuttrycket.

```html
<div class="columns is-multiline">
    <form @submit.prevent="validateForm('form-1')" class="columns column is-multiline is-12" data-vv-scope="form-1">
        <legend>Form 1</legend>
        <div class="column is-12">
            <label class="label">Email</label>
            <p class="control has-icon has-icon-right">
                <input name="email" v-validate="'required|email'" :class="{'input': true, 'is-danger': errors.has('form-1.email') }" type="text" placeholder="Email">
                <i v-show="errors.has('form-1.email')" class="fa fa-warning"></i>
                <span v-show="errors.has('form-1.email')" class="help is-danger">{{ errors.first('form-1.email') }}</span>
            </p>
        </div>
        <div class="column is-12">
            <label class="label">Password</label>
            <p class="control has-icon has-icon-right">
                <input name="password" v-validate="'required|min:6'" :class="{'input': true, 'is-danger': errors.has('form-1.password') }" type="password" placeholder="Password">
                <i v-show="errors.has('form-1.password')" class="fa fa-warning"></i>
                <span v-show="errors.has('form-1.password')" class="help is-danger">{{ errors.first('form-1.password') }}</span>
            </p>
        </div>
        <div class="column is-12">
            <p class="control">
                <button class="button is-primary" type="submit" name="button">Sign up</button>
                <button class="button is-danger" type="button" name="button" @click="errors.clear('form-1')">Clear</button>
            </p>
        </div>
    </form>
    <form @submit.prevent="validateForm('form-2')" class="columns column is-multiline is-12" data-vv-scope="form-2">
        <legend>Form 2</legend>
        <div class="column is-12">
            <label class="label">Email</label>
            <p class="control has-icon has-icon-right">
                <input name="email" v-validate="'required|email'" :class="{'input': true, 'is-danger': errors.has('form-2.email') }" type="text" placeholder="Email">
                <i v-show="errors.has('form-2.email')" class="fa fa-warning"></i>
                <span v-show="errors.has('form-2.email')" class="help is-danger">{{ errors.first('form-2.email') }}</span>
            </p>
        </div>
        <div class="column is-12">
            <label class="label">Password</label>
            <p class="control has-icon has-icon-right">
                <input name="password" v-validate="'required|min:6'" :class="{'input': true, 'is-danger': errors.has('form-2.password') }" type="password" placeholder="Password">
                <i v-show="errors.has('form-2.password')" class="fa fa-warning"></i>
                <span v-show="errors.has('form-2.password')" class="help is-danger">{{ errors.first('form-2.password') }}</span>
            </p>
        </div>
        <div class="column is-12">
            <p class="control">
                <button class="button is-primary" type="submit" name="button">Sign up</button>
                <button class="button is-danger" type="button" name="button" @click="errors.clear('form-2')">Clear</button>
            </p>
        </div>
    </form>
</div>
```

```js
xport default {
  name: 'scopes-example',
  methods: {
    validateForm(scope) {
      this.$validator.validateAll(scope).then((result) => {
        if (result) {
          // eslint-disable-next-line
          alert('Form Submitted!');
        }
      });
    }
  }
};
```

## Async-validering

Låt oss säga att du vill validera något specifikt för din appdomän som inte tillhandahålls av standardvalidatorerna.

Till exempel kan vi validera en användarkupong vid kassan. Om det är en giltig kupong så rabatt du det för honom, om inte han betalar det fulla priset.

Här är en lista över våra giltiga kuponger: `SUMMER2016`, `WINTER2016` och `FALL2016`.

Var och en som ger 20% rabatt. Valideringsprocessen är följande: vi tar in inmatningen och skickar den till backend, svaret bör avgöra om kupongen är giltig vilket är upp till dig.Här simulerar jag async beteende med `setTimeout`.

Vee-Validate allows the usage of async validators, but it requires them to return a promise that resolves with an object containing the property `valid` vilket borde motsvara ett booleskt tillstånd i valideringsstatusen.

**html**
```html
<div class="columns is-multiline">
    <div class="column is-12">
        <span :class="{ 'discounted': discounted }">Price: {{ price }}$</span>
        <span v-show="discounted" class="SeemsGood">{{ discountedPrice }}$</span>
    </div>
    <form @submit.prevent="applyCoupon" class="column is-12">
        <label class="label">Coupon</label>
        <p class="control has-icon has-icon-right">
            <input v-model="coupon" name="coupon" :class="{'input': true, 'is-danger': errors.has('coupon') }" type="text" placeholder="Enter Your Coupon">
            <i v-show="errors.has('coupon')" class="fa fa-warning"></i>
            <span v-show="errors.has('coupon')" class="help is-danger">{{ errors.first('coupon') }}</span>
        </p>

        <p class="control">
            <button type="submit" class="button is-primary" name="button">Apply</button>
        </p>
    </form>
</div>
```

**js**
```js
import { Validator } from 'vee-validate';

export default {
  name: 'coupon-example',
  data: () => ({
    coupon: '',
    price: 100,
    discounted: false
  }),
  computed: {
    discountedPrice() {
      return this.discounted ? this.price - (0.2 * this.price) : this.price;
    }
  },
  methods: {
    applyCoupon() {
      this.$validator.validate('coupon', this.coupon).then((result) => {
        this.discounted = result;
      });
    }
  },
  created() {
    Validator.extend('verify_coupon', {
      getMessage: field => `The ${field} is not a valid coupon.`,
      validate: value => new Promise((resolve) => {
        // API call or database access.
        const validCoupons = ['SUMMER2016', 'WINTER2016', 'FALL2016'];

        setTimeout(() => {
          resolve({
            valid: value && validCoupons.indexOf(value.toUpperCase()) !== -1
          });
        }, 500);
      })
    });
    this.$validator.attach({
      name: 'coupon',
      rules: 'required|verify_coupon'
    });
  }
};
```

## Radio Buttons

Vee-validate stöder också validering av radioknappar, du kan använda vilka regler du vill men, endast få regler är meningsfulla, som `require`.

En sak att notera i det här exemplet är att du bara behöver använda direktivet på en av radioknapparna, du behöver inte fästa den på alla, alla måste dock ha samma namn.

I det följande exemplet ingår inte det tredje värdet med regeln `in:1,2`

**html**
```html
<form @submit.prevent="validateForm">
    <div class="columns is-multiline">
        <div class="column is-6">
            <legend :class="{ 'error': errors.has('radio_group_1') }">Radio Group 1</legend>
            <p class="control">
                <label class="radio">
                    <input name="radio_group_1" v-validate="'required|in:1,2'" value="1" type="radio">
                    Option 1
                </label>
                <label class="radio">
                    <input name="radio_group_1" value="2" type="radio">
                    Option 2
                </label>
                <label class="radio">
                    <input name="radio_group_1" value="3" type="radio">
                    Option 3
                </label>
            </p>
            <span class="help is-danger" v-show="errors.has('radio_group_1')">{{ errors.first('radio_group_1') }}</span>
        </div>

        <div class="column is-6">
            <legend :class="{ 'error': errors.has('radio_group_2') }">Radio Group 2 (Bound)</legend>
            <p class="control">
                <label class="radio">
                    <input v-model="radio_group_2" v-validate="{ rules: 'required|in:1,2', arg: 'radio_group_2' }" name="radio_group_2" value="1" type="radio">
                    Option 1
                </label>
                <label class="radio">
                    <input v-model="radio_group_2" name="radio_group_2" value="2" type="radio">
                    Option 2
                </label>
                <label class="radio">
                    <input v-model="radio_group_2" name="radio_group_2" value="3" type="radio">
                    Option 3
                </label>
            </p>
            <span class="help is-danger" v-show="errors.has('radio_group_2')">{{ errors.first('radio_group_2') }}</span>
        </div>
    </div>

    <p class="control">
        <button type="submit" class="button is-primary" name="button">Apply</button>
    </p>
</form>
```
**JS**
```js
export default {
  name: 'radio-buttons-example',
  data: () => ({
    radio_group_2: '',
  }),
  methods: {
    validateForm() {
      this.$validator.validateAll().then((result) => {
        if (result) {
          // eslint-disable-next-line
          alert('All Passes!');
          return;
        }
        alert('Oh NO!');
      });
    }
  }
};
```

## Checkbox Example

Vee-validate stöder också validering av kryssrutor. Men som radioknappar begränsas stödets omfattning av ingående naturen, men alla regler fungerar oavsett.

Precis som för radioknappar behöver du bara bifoga valideringsdirektivet och attributen i kryssrutan under validering.

If there are multiple checkboxes (group), you only have to add the directive on one of them. Om flera värden väljs väljer validatorn valideringarna i varje kryssruta.

I följande exempel är den mest grundläggande användningen av kryssrutor, validering dav villkoravtal som ingen läser.

**html**
```html
div>
    <div class="terms">
        <p>
            {{ terms }}
        </p>
    </div>
    <div>
        <p class="control">
            <label class="checkbox">
                <input name="terms" v-validate="'required'" type="checkbox">
                I agree to the terms and conditions.
            </label>
            <span class="help is-danger" v-show="errors.has('terms')">{{ errors.first('terms') }}</span>
        </p>
    </div>
    <p class="control">
        <button type="button" class="button is-primary" @click="nextStep">Next</button>
    </p>
</div>
```

**JS**
```js
const terms = 'Lorem ipsum dolor sit amet, consectetur adipisicing elit ';

export default {
  name: 'checkbox-example',
  data: () => ({
    terms: terms.repeat(20)
  }),
  methods: {
    nextStep() {
      this.$validator.validateAll().then((result) => {
        if (result) {
          alert('You just agreed to conditions without reading it.');
          return;
        }
        // eslint-disable-next-line
        alert('You actually did not agree?');
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