---
date: '2017-02-14 11:48 +0100'
published: false
title: React - Building a Wordpress SPA
category:
  - Development
---
Over the course of this document, I’ll be putting together a simple JavaScript app that uses the REST API to power its content. 

We’ll be using [WordPress 4.5.3](https://wordpress.org/wordpress-4.5.3.zip) running on a local development environment, version 2 of the REST API as a plugin, React. 

---

## Local Setup

We’ll kick things off by getting some core data into a local install of WordPress, to testing the basic reading and writing functionality of the REST API.

For the sake of ease,Use the free version of [DesktopServer](https://serverpress.com/get-desktopserver/) to bootstrap your WP site and add some dummie posts. 

Go to plugins ivia the back end and search for rest api and install WP REST API. 