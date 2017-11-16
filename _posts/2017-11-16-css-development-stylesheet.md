---
date: '2017-11-16 20:16 +0100'
published: true
title: CSS - Development stylesheet
---
Det här är en stylesheet som jag använder vid utveckling. Det ser ut som en blueprint. 

*Den är fortfarande under utveckling och har tankar kring att lägga upp den för konsumering när jag närmar mig något som är redo för release.*

```css
html, body { margin: 0; padding: 0; }

body {
  margin: 0 auto;
  padding: 0px;
  min-width: 230px;
  max-width: 550px;

  font: 10px 'Helvetica Neue', Helvetica, Arial, sans-serif;
  color: white;
  line-height: 1.4em;

  -webkit-font-smoothing: antialiased;
  -moz-font-smoothing: antialiased;
  font-smoothing: antialiased;
  font-weight: 300;
}

.background {
  /* Background */
	background-color: rgb(0, 119, 204);
  background-image: linear-gradient(0deg, transparent, transparent 7px, rgba(255, 255, 255, 0.25) 7px), linear-gradient(90deg, transparent, transparent 7px, rgba(255, 255, 255, 0.25) 7px);
  background-size: 8px 8px;
  background-position: left top;
}

section, div {
  color: rgb(255, 255, 255);
  background-color: rgba(255, 255, 255, 0.125);
  outline: rgba(255, 255, 255, 0.5) solid 1px !important;
  padding: 20px;
}

a {
  width: 100%;
  text-align: center;
  color: white;
  text-decoration: none;
  margin-right: 5px;
}

li {
  float: left;
  list-style-type: none;
}

.group:after {
  content: "";
  display: table;
  clear: both;
}

label {
  float: left;
  text-align: left;
  margin-right: 10px;
  width: 60px;
}

input {
  border: 1px solid rgba(255, 255, 255, 0.5);
  background-color: rgba(255, 255, 255, 0.125);
  outline: none;
  color: white;
  font-size: 16px;
}

input::placeholder {
  font-size: 16px;
  color: rgba(255, 255, 255, 0.5);
}

button {
  color: #ffffff;
  font-size: 16px;
  background: rgba(255, 255, 255, 0.125);
  padding: 10px 20px 10px 20px;
  text-decoration: none;
  border: none;
  width: 100%;
}

button:hover {
  background: rgba(255, 255, 255, 0.5);
  text-decoration: none;
}

```

```html
<span style="position: fixed;bottom: 10px;right: 10px;font-size: 15px;">made by DevelDoe</span>
```