---
date: '2016-11-18 14:25 +0100'
published: false
title: Jekyll - Integrating Webpack & React
---
In this article we'll do a step by step walk through on how to create a basic jekyll-webpack integration.

## Integrating Jekyll and Webpack

### Setting up your dev environment

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

### Rearrange your project structure

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
├── src
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

This requires that you move all of the content you want Jekyll to pick up for the build process to a src directory in the root, including the index.html. Keep the config.yml in the root. You’ll need to let Jekyll know you’re building from src into public:


```yml
destination: public
source: src
```

Because you’re building to a public folder, you can use [Pow](http://pow.cx/) to serve the static site at http://your-project-name.dev, which is nice for development.

