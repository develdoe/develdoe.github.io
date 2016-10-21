---
date: '2016-10-21 12:04 +0200'
published: true
title: React - Starter Pack
category:
  - react
---
*This article is a re-write of the orignal article at [facebook.github.io](https://facebook.github.io/react/docs/getting-started.html). I wrote it for learning purposes and made small edits to better the article.*

The starter kit includes prebuilt copies of React and React DOM for the browser, as well as a collection of usage examples to help you get started.

Download the [starter kit](https://facebook.github.io/react/downloads/react-15.3.2.zip)

In the root directory, create a helloworld.html with the following contents:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>Hello React!</title>
    <script src="build/react.js"></script>
    <script src="build/react-dom.js"></script>
    <script src="https://unpkg.com/babel-core@5.8.38/browser.min.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/babel">
      ReactDOM.render(
        <h1>Hello, world!</h1>,
        document.getElementById('example')
      );
    </script>
  </body>
</html>
```

The XML syntax inside of JavaScript is called JSX; check out the [JSX syntax](https://facebook.github.io/react/docs/jsx-in-depth.html) to learn more about it. In order to translate it to vanilla JavaScript we use <script type="text/babel"> and include Babel to actually perform the transformation in the browser. Open the html from a browser and you should already be able to see the greeting!

## Separate File

Your React JSX code can live in a separate file. Create the following src/helloworld.js:

```javascript
ReactDOM.render(
  <h1>Hello, world!</h1>,
  document.getElementById('example')
);
```

Then reference it from helloworld.html:

```javascript
<script type="text/babel" src="src/helloworld.js"></script>
```

**Some browsers (Chrome, e.g.) will fail to load the file unless it's served via HTTP.**



