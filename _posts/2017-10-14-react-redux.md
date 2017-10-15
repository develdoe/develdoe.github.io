---
date: '2017-10-14 17:35 +0200'
published: true
title: React - Redux
---
## Usage with React

Redux fungerar som sagt särskilt bra med bibliotek som React, eftersom de låter dig beskriva användargränssnitt som en funktion av state, och Redux avger tillståndsuppdateringar som svar på åtgärder.

## Installing React Redux

Reaktionsbindningar ingår inte som standard i Redux. Du måste installera dem explicit:

```bash
npm install --save react-redux
```

## Presentational and Container Components

React bindningar för Redux omfamnar tanken på att separera presentations- och behållarkomponenter.
