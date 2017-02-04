---
date: '2017-02-04 02:07 +0100'
published: true
title: CSS - setup css file for react projects
category:
  - CSS
---
I use this file to bootstrap my react projects. 

Its simply a css reset with some initial rendering defaults for things like components. This goes well with the setup I use when I use [React Routing](http://develdoe.com/2016/react-routing/)

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
