---
date: '2016-10-14 23:11 +0200'
published: true
title: React - Create React App
category:
  - react
---
The original article can be found at [facebook.github.io](https://facebook.github.io/react/docs/getting-started.html). In this article I am attempting to simplify the article by making it shorter and more to the point. However the original article is already quite simple as it is.

If you’re getting started with React, use create-react-app to automate the build of your app. There is no configuration file, and react-scripts is the only extra build dependency in your package.json. Your environment will have everything you need to build a modern React app:

* React, JSX, and ES6 support.
* Language extras beyond ES6 like the object spread operator.
* A dev server that lints for common errors.
* Import CSS and image files directly from JavaScript.
* Autoprefixed CSS, so you don’t need -webkit or other prefixes.
* A build script to bundle JS, CSS, and images for production, with sourcemaps.

*The feature set doesn’t support advanced features such as server rendering or CSS modules. The tool is also non-configurable because it is hard to provide a cohesive experience and easy updates across a set of tools when the user can tweak anything.

You don’t have to use this. Historically it has been easy to gradually adopt React. However many people create new single-page React apps from scratch every day. We’ve heard loud and clear that this process can be error-prone and tedious, especially if this is your first JavaScript build stack. This project is an attempt to figure out a good way to start developing React apps.*

## Philosophy

* **One Dependency**: There is just one build dependency that uses other amazing projects and provides a cohesive experience on top.
* **Zero Configuration**: There are no configuration files or command line options.
* **No Lock-In**: You can “eject” to a custom setup at any time. Run a single command, and all the configuration and build dependencies will be moved directly into your project.

## What’s Inside

The tools used by Create React App are subject to change. Currently it is a thin layer on top of many amazing community projects, such as:

* webpack with webpack-dev-server, html-webpack-plugin and style-loader
* Babel with ES6 and extensions used by Facebook (JSX, object spread, class properties)
* Autoprefixer
* ESLint
* Jest
* and others.

All of them are transitive dependencies of the provided npm package.

## Installation

Install globaly:

```bash
npm install -g create-react-app
```

*The Node installation is only required for the build tools that rely on it locally and not a Node backend.*

New app:

```bash
create-react-app my-app
cd my-app
```

It will create a directory called my-app inside the current folder. Inside, it will generate the initial project structure and install the dependencies:

```bash
my-app/
  README.md
  node_modules/
  package.json
  .gitignore
  public/
    favicon.ico
    index.html
  src/
    App.css
    App.js
    App.test.js
    index.css
    index.js
    logo.svg
```

No configuration or complicated folder structures, just the files you need to build your app.
Once the installation is done, you can run some commands inside the project folder.

Run in development mode:

```bash
$ npm start
```

Open http://localhost:3000 to view it in the browser. 

The page will reload if you make edits. You will see the build errors and lint warnings in the console.

Run the test watcher in an interactive mode:

```bash
$ npm test
```

By default, runs tests related to files changes since the last commit. [Read more about testing](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md#running-tests).

Builds the app for production to the build folder:

```bash
$ npm run build
```

Bundles React in production mode and optimizes the build for the best performance. The build is minified and the filenames include the hashes. Your app is ready to be deployed!

## User Guide

The [User Guide](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md) includes information on different topics.

*A copy of the user guide will be created as README.md in your project folder.*

## Converting to a Custom Setup

If you’re a power user and you aren’t happy with the default configuration, you can “eject” from the tool and use it as a boilerplate generator.

Running `npm run eject` copies all the configuration files and the transitive dependencies right into your project so you have full control over them. Commands like npm start and npm run build will still work, but they will point to the copied scripts so you can tweak them. At this point, you’re on your own.

**Note that once you eject, you can’t go back!**

You don’t have to ever use eject. The curated feature set is suitable for small and middle deployments, and you shouldn’t feel obligated to use this feature. However we understand that this tool wouldn’t be useful if you couldn’t customize it when you are ready for it.

## Limitations

Some features are currently not supported:

* Server rendering.
* Some experimental syntax extensions (e.g. decorators).
* CSS Modules.
* LESS or Sass.
* Hot reloading of components.

Some of them might get added in the future if they are stable, are useful to majority of React apps, don’t conflict with existing tools, and don’t introduce additional configuration.

## Alternatives

* [insin/nwb](https://github.com/insin/nwb)
* [mozilla/neo](https://github.com/mozilla/neo)
* [NYTimes/kyt](https://github.com/NYTimes/kyt)

You can also use module bundlers like [webpack](http://webpack.github.io/) and [Browserify](http://browserify.org/) directly.
React documentation includes a [walkthrough](https://facebook.github.io/react/docs/package-management.html) on this topic.
