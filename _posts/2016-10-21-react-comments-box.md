---
date: '2016-10-21 12:51 +0200'
published: true
title: React - Comments Box
category:
  - react
---
This article is a re-write of the orignal article at [facebook.github.io](https://facebook.github.io/react/docs/tutorial.html). I wrote it for learning purposes and made small edits to better the article.

We'll be building a basic version of the realtime comments system offered by Disqus, LiveFyre or Facebook comments.

We'll provide:

* A **view** of all of the comments
* A form to **submit** a comment
* **Hooks** for you to provide a custom backend

It'll also have a few neat features:

* **Optimistic commenting**: comments appear in the list before they're saved on the server so it feels fast.
* **Live updates**: other users' comments are popped into the comment view in real time.
* **Markdown formatting**: users can use Markdown to format their text.

## Running a server

In order to start this tutorial, we're going to require a running server. This will serve purely as an API endpoint which we'll use for getting and saving data. In order to make this as easy as possible, we've created a simple server in a number of scripting languages that does exactly what we need it to do. [Download](https://github.com/reactjs/react-tutorial/archive/master.zip) a zip file containing everything needed to get started.

For sake of simplicity, the server we will run uses a JSON file as a database. You would not run this in production but it makes it easy to simulate what you might do when consuming an API. Once you start the server, it will support our API endpoint and it will also serve the static pages we need.

