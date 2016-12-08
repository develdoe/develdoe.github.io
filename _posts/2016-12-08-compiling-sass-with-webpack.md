---
date: '2016-12-08 09:23 +0100'
published: true
title: Compiling SASS with Webpack
category:
  - dev
---
Webpack is the Swiss-army knife of Web bundler. It can  handle SASS compilation for you, among many other things. Let’s see how it works...

**Install**

```bash
npm install --save-dev sass-loader css-loader style-loader
```

**Add a new loader**

```js
module.exports = {
    // ...
    module: {
        loaders: [
            // ...
            {
                test: /\.scss$/,
                loaders: ['style', 'css', 'sass']
            }
        ]
    }
}
```