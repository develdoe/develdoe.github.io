---
date: '2016-11-25 15:37 +0100'
published: true
title: React - Dependencies with npm & Webpack
category:
  - react
---
In this article we are going to explore how to create an React environment with npm.

This improves resource management compared to the vary basic approach discussed in [Simple Code Example](http://develdoe.com/2016/react-simple-code-example/).

```bash
$ npm install -g webpack@1.12.13
```

```bash
$ npm install --save react@0.14.7 react-dom@0.14.7
```

```bash
$ npm install --save-dev webpack@1.12.13 babel-core@6.5.1 babel-loader@6.2.2 babel-preset-es2015@6.5.0 babel-preset-react@6.5.0
```