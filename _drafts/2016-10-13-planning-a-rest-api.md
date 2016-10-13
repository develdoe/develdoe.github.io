---
date: '2016-10-13 17:49 +0200'
published: true
title: Planning a REST API
---
The final result of this article will be all the information you need to start the development process of an REST API. This article is going to use a bookstore API, as an example API.

## The case

Let’s call our fake bookstore Come&Read and assume that we’ve been asked to create a distributed API that will bring the bookstore into the twenty-first century.

Right now, it’s a pretty decent business. The bookstore currently has 10 different points of sale located across the United States. The company leadership is considering expanding into even more states. The current main problem, though, is that all of those stores have barely entered the digital era. The way of working and recordkeeping is very manual and heterogeneous; for instance:

* Some of the smaller stores keep records on paper and send a manually typed weekly report to the head store.
* While the bigger locations tend to use some sort of CRM software, there is no standard as long as numbers are exported into a common format and sent in a weekly report.
* Based on the weekly reports, the head store handles inventory of the chain-wide matters (store-specific stock, global stock, sales both per-store and global, employee records, etc.).
* Overall, the bookstore lacks web interaction with its customers, which a twenty-first century business must have. Its web site only lists addresses and phone numbers, and nothing more.

All of the secondary stores are connected by a very thin line to the main store.

The goal is to grow as a business, by not only opening new stores across the country but by also strengthening the bond between all the stores. And to achieve this, the backbone of everything will be our API. Our system will have to be a decentralized one, meaning that you’ll treat the main store just like any other store, and provide a common set of tools and data sources for every client application that might come in the future, instantly allowing for such things as the following:

* Cross-store searches
* Automatic control of global stock
* Automatic control over sales on a global level
* Dynamic data sources for things like web sites and mobile apps

The new system lives in the cloud, with all stores connected directly to it. The bond is stronger now, since everything is done automatically and every piece of information is available to all stores. Also, this new API-based system allows for the easy development of new ways to interact with potential customers, including mobile apps and dynamic web sites.

## The Specifications
