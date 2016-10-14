---
date: '2016-10-14 11:12 +0200'
published: false
title: Developing A REST API with Node.js
---
This article will cover the directory structure, through the small design decisions made during development, and finally to the code itself.

This article will display the entire source code for the example API shown in the article [Planning a REST API]('http://develdoe.com/2016/planning-a-rest-api/'). 

We’ll go over the relevant parts. Sadly, some bits and pieces are just plain boring (like the JSON Schema definitions and the simpler models), so I’ll skip it. These things should be pretty self-explanatory to developers anyway, no matter the level of expertise. I’ll cover the development stage as follows:

* Minor simplifications/design decisions made during development.
* Folder structure, it’s important to understand where everything is and why.
* The code itself, file by file, including explanation when needed.

## Minor Changes