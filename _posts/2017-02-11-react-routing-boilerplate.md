---
date: '2017-02-11 00:01 +0100'
published: true
title: React - Routing Boilerplate
category:
  - React
---
**/public/index.html**

```html
<!DOCTYPE html>
<html>
<head>

    <meta charset=utf-8>

    <link rel=stylesheet media=all href=setup.css>

</head>

<body>

    <div id=app></div>

    <script src=bundle.js></script>
```

**/public/setup.css**

```css
html, body, div, span, applet, object, iframe,
h1, h2, h3, h4, h5, h6, p, blockquote, pre,
a, abbr, acronym, address, big, cite, code,
del, dfn, em, img, ins, kbd, q, s, samp,
small, strike, strong, sub, sup, tt, var,
b, u, i, center,
dl, dt, dd, ol, ul, li,
fieldset, form, label, legend,
table, caption, tbody, tfoot, thead, tr, th, td,
article, aside, canvas, details, embed,
figure, figcaption, footer, header, hgroup,
menu, nav, output, ruby, section, summary,
time, mark, audio, video {
	margin: 0;
	padding: 0;
	border: 0;
	font-size: 100%;
	font: inherit;
	vertical-align: baseline;
}
/* HTML5 display-role reset for older browsers */
article, aside, details, figcaption, figure,
footer, header, hgroup, menu, nav, section {
	display: block;
}
body {
	line-height: 1;
}
ol, ul {
	list-style: none;
}
blockquote, q {
	quotes: none;
}
blockquote:before, blockquote:after,
q:before, q:after {
	content: '';
	content: none;
}
table {
	border-collapse: collapse;
	border-spacing: 0;
}
*, *:before, *:after {
 box-sizing: border-box;
}
html,body,#app,#main{
	height: 100%;
	width: 100%;
}
html {
  box-sizing: border-box;
}
#nav{
	height: 10%;
}
.component{
	padding: 15px;
}
#main{
	margin: 0;
	padding: 0;
}
a{
	margin-right: 5px;
}
.page{
	height: 90%;
}
```

**/package.json**

```json
{
  "name": "react-routing-boilerplate",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Andree DevelDoe Ray",
  "license": "ISC",
  "dependencies": {
    "axios": "^0.15.3",
    "express": "^4.14.1",
    "react": "^0.14.7",
    "react-dom": "^0.14.7",
    "react-router": "^3.0.2"
  },
  "devDependencies": {
    "babel-core": "^6.5.1",
    "babel-loader": "^6.2.2",
    "babel-preset-es2015": "^6.5.0",
    "babel-preset-react": "^6.5.0",
    "babel-preset-stage-0": "^6.22.0"
  }
}
```

