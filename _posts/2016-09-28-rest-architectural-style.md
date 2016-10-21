---
date: '2016-09-28 09:27 +0200'
published: true
title: REST Architectural Style
category:
  - dev
---
Just because you can interact with a system using HTTP, and send JSON back and forth, doesn’t mean it’s a RESTful system. REST is a lot more than that.

REST is protocol independent, but for the sake of this article and since we’re focusing on API design, let’s assume that the protocol we’re using is HTTP, which will simplify explanations and examples.

The term REST, is an architectural style for distributed hypermedia systems. Put simply, REST (short for Representational State Transfer) is an architectural style defined to help create and organize distributed systems. An important aspect of REST is that it is an architectural style—not a guideline, not a standard, or anything that would imply that there are a set of hard rules to follow in order to end up having a RESTful architecture.

It, it’s subject to misinterpretations from the people reading about it. Not only that, but some go as far as to leave parts out, and implement a subset of its features.

The main idea behind REST is that a distributed system, organized RESTfully, will improve in the following areas:
 
* **Performance:** The communication style proposed by REST is meant to be efficient and simple, allowing a performance boost on systems that adopt it.

* **Scalability of component interaction:** Any distributed system should be able to handle this aspect well enough, and the simple interaction proposed by REST greatly allows for this.

* **Simplicity of interface:** A simple interface allows for simpler interactions between systems, which in turn can grant benefits like the ones previously mentioned.

* **Modifiability of components:** The distributed nature of the system, and the separation of concerns proposed by REST allows for components to be modified independently of each other at a minimum cost and risk.

* **Portability**: REST is technology and language agnostic, meaning that it can be implemented and consumed by any type of technology.

* **Reliability:** The stateless constraint proposed by REST allows for the easier recovery of a system after failure.

* **Visibility:** The stateless constraint proposed has the added benefit of improving visibility, because any monitoring system doesn’t need to look further than a single request message to determine the full state of said request.

A component-centric design allows you to make systems that are very fault tolerant. Having the failure of one component not affect the entire stability of the system is a great benefit for any system. Interconnecting components is quite easy, minimizing the risks when adding new features or scaling up or down. 

A system designed with REST in mind will be accessible to a wider audience, thanks to its portability. With a generic interface, the system can be used by a wider range of developers. 

In order to achieve these properties and benefits, a set of constraints were added to REST to help define a uniform connector interface.