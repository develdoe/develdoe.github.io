---
date: '2016-11-18 14:25 +0100'
published: true
title: Jekyll - Integrating Webpack & React
category:
  - jek
---
This article guides you on the steps to create a basic jekyll-webpack integration that you can use to get React working with Jekyll.

## Environment

Node:

```bash
$ sudo install npm -g
```

Webpack:

```bash
$ npm install webpack -g
```

Jekyll:

```bash
$ gem install jekyll -g
```

## Structure

Original:

```yml
.
├── _config.yml
├── _includes
│ ├── …
├── _layouts
│ ├── default.html
│ ├── page.html
│ └── post.html
├── _posts
│ ├── …
├── _sass
│ ├── _base.scss
│ ├── _layout.scss
│ └── _syntax-highlighting.scss
├── about.md
├── css
│ └── main.scss
├── feed.xml
└── index.html
```

New

```yml
.
├── _config.yml
├── package.json
├── public
│ ├── …
├── source
│ ├── _includes
│ │ ├── …
│ ├── _layouts
│ │ ├── default.html
│ │ ├── page.html
│ │ └── post.html
│ ├── _posts
│ │ ├── …
│ ├── _sass
│ │ ├── …
│ ├── about.md
│ ├── assets
│ │ ├── css
│ │ ├── images
│ │ └── javascripts
│ │ └── bundle.js
│ ├── feed.xml
│ └── index.html
├── webpack
│ ├── entry.js
│ └── components
│ └── …
└── webpack.config.js
```

## Configuration

_config.yml:

```yml
destination: public
source: source
```

## Packages


Init:

```bash
$ npm init in the root
```

## Webpack

Create a webpack directory in the root project with an entry.js file, and, remove any Jekyll javascript **files** from your assets directory. 

Create a webpack.config.js in the root:

```js
module.exports = {
  // webpack folder’s entry js — excluded from jekll’s build process.
  entry: './webpack/entry.js',
  output: {
    // we’re going to put the generated file in the assets folder so jekyll will grab it.
    path: 'src/assets/javascripts/',
    filename: 'bundle.js'
  },
  module: {
  loaders: [
    {
      test: /\.jsx?$/,
      exclude: /(node_modules)/,
      loader: 'babel', // ‘babel-loader’ is also a legal name to reference
      query: {
        presets: ['react', 'es2015']
      }
    }
    ]
  }
};
```

## Ignore

You need to tell both Github and Jekyll to ignore node_modules/.

.gitignore:

```
.sass-cache
.jekyll-metadata
public
src/assets/bundle.js
node_modules
```

_config.yml:

```yml
exclude: ['node_modules']
```

## Include

_layouts/default.html:

```html
<!DOCTYPE html>
<html>
 …
 <body>
 …
<script type="text/javascript" src="/assets/javascripts/bundle.js" charset="utf-8"></script>
</body>
</html>
```
## Dependencies

```bash
$ npm install webpack babel-core babel-loader babel-preset-es2015 babel-preset-react react react-addons-update react-dom --save-dev
```

## Run 

```bash
$ webpack
$ jekyll serve
```
