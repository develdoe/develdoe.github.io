---
title: Firebase och React
layout: post
category: react
---

*Denna artikel är en förkortning och översättning av artikeln "Intro to Firebase and React"  som du hittar hos CSS Tricks, "https://css-tricks.com/intro-firebase-react/"*

**Den här artikeln förutsätter att du redan har viss grundläggande kunskaper om hur React fungerar.**

## Vad är Firebase?

Googles Firebase är en cloud-based databas värdtjänst där du kan hosta en databas. De erbjuder dig verktygen att interagera med den. Du kan använda den för att lagra och hämta data i realtid. 

Utöver det så kan firebase göra  fler saker som hantering användarautentisering och lagra filer.

### Hur lagrar Firebase data?

Firebase lagrar data som ett jätteobjekt med nyckelvärdespar. Till skillnad från JSON eller JavaScript-objekt finns det inga arrayer i Firebase.

En Firebase-databas kan se ut så här:

```
{
      "groceries": {
        "-KjQTqG3R2dPT8s2jylW": "tomato",
        "-KjQTrds1feHT3GH_29o": "pasta",
        "-KjQTsmfBR8zN1SwPPT8": "milk",
        "-KjQTtnzt_jJZPoCHWUM": "sugar"
      },
      "users": {
        "name": {
          "-KjQTyIfKFEVMYJRZ09X": "simon",
          "-KjQU-Xuy5s7I-On9rYP": "ryan",
          "-KjQU0MYVeKRsLuIQCYX": "sylvia"
        }
      }
}
```

Du hittar mer information kring struktureringen i Firebase i dess [dokumentation](https://firebase.google.com/docs/database/web/structure-data). 

## Bootstrap Project

Börja med att bootstrappa ett React projekt. Jag har ett projekt som heter [DevelStrap](https://github.com/AndreeDeveldoeRay/DevelStrap), men du kan använda vilket som. 

```bash
git clone https://github.com/AndreeDeveldoeRay/DevelStrap.git
cd DevelStrap
npm install
npm install firebase
npm start
```

Open new terminal window or tab:

```bash
webpack -w 
```


## Firebase Database

Nu när vår app är konfigurerad måste vi skapa ett konto och en databas på Firebase så att vi kan länka upp vår applikation till den.

[Gå vidare till Firebase hemsida](https://firebase.google.com/) och klicka på "Get Started" 

Detta tar dig till en sida där du kommer att bli bedd att autentisera med ditt Google-konto. Välj det konto som du vill att projektet ska anslutas till och tryck på OK.

Detta bör ta dig till Firebase-konsolen, som ser något ut så här:

![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_1000,f_auto,q_auto/v1496406458/firebase-dashboard_gigmqp.png)

Skapa en ny data bas genom att klicka på "Add new project" och ge det ett namn. 

Det tar dig till din apps instrumentpanel, som ser ut så här:

![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_1000,f_auto,q_auto/v1496406510/firebase-overview_xa2kp0.png)

Eftersom vi bygger en webapp väljer du "Add Firebase to your web app". Vilket kommer att utlösa en popup med någon kod som ser ut så här:

```html
<script src="https://www.gstatic.com/firebasejs/4.2.0/firebase.js"></script>
<script>
  // Initialize Firebase
  var config = {
    apiKey: "AIzaSyDHXPH6kH9wevpz3WRVDcmyuxPLdwCiHigh",
    authDomain: "fir-d45cd5.firebaseapp.com",
    databaseURL: "https://fir-d45sc5.firebaseio.com",
    projectId: "fir-d445c5",
    storageBucket: "",
    messagingSenderId: "1022982s3456205754"
  };
  firebase.initializeApp(config);
</script>
```

Eftersom vi importerar Firebase till vårt projekt med ES6-moduler behöver vi inte skript taggarna. Konfigurationsobjektet är viktigt, eftersom det är så vi verifierar vår React-applikation mot vår Firebase-databas.

## Koppla upp vår App mot Firebase

Kopiera det hela konfigurationsobjektet från Firebase. 

Skapa sedan en ny mapp i `src` mappen som du döper till `firebase`. I den mappen skapar vi en fil med namnet `index.jsx`. 

I den nya filen skall vi importera Firebase, vårt konfigurationsobject och initialisera vår app.

**./src/firebase/index.jsx**

```
import Firebase from 'firebase'

var config = {
    apiKey: "AIzaSyDHXPH6kH9wevpz3WRVDcmyuxPLdwCiHigh",
    authDomain: "fir-d45cd5.firebaseapp.com",
    databaseURL: "https://fir-d45sc5.firebaseio.com",
    projectId: "fir-d445c5",
    storageBucket: "",
    messagingSenderId: "1022982s3456205754"
  }

firebase.initializeApp(config)

export default firebase
```

En sista sak som vi behöver göra innan vi kan dyka in i grovkörning av vår App. Vi måste tillfälligt inaktivera autentiseringskrav så att vi kan lägga till och ta bort objekt utan att behöva ha någon typ av användarautentiseringsflöde.

I Firebase Dashboard, till vänster om skärmen, märker du att det finns en flik "Databas". Klicka på den. Sedan, på höger sida, under realtidsdatabasen underrubrik ser du en flik för "Regler".  Där hittar du en uppsättning regler som ser ut något som:

```
{
    "rules": {
      ".read": "auth != null",
      ".write": "auth != null"
    }
}
```

Vi måste ställa in `.read` och `.write`  till sanna, så att vi kan lägga till och läsa data utan att authentisera oss. När du är klar ska det se ut så här:

![](https://res.cloudinary.com/css-tricks/image/upload/c_scale,w_1000,f_auto,q_auto/v1496406627/firebase-rules_sjfzyy.png)