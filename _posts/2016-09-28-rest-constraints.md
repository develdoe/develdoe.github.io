---
date: '2016-09-28 09:52 +0200'
published: true
title: REST Constraints
category:
  - Development
---
There are two ways to define a system.

One is to start from a blank slate, an empty whiteboard, with no initial knowledge of the system being built or the use of familiar components until the needs are satisfied.

A second approach is to start with the full set of needs for the system, and constraints are added to individual components until the forces that influence the system are able to interact in harmony with each other.

REST follows the second approach. In order to define a REST architecture, a null-state is initially defined. a system that has no constraints whatsoever and where component differentiation is nothing but a myth, and constraints are added one by one.

### Client-Server
The first constraint to be added is one of the most common ones on network-based architectures: client - server. A server is in charge of handling a set of services, and it listens for requests regarding said services. The requests, in turn, are made via a connector by a client system needing one of those services

The main principle behind this constraint is the separation of concerns. It allows for the separation of front-end code from the server side code, which should take care of storage and server-side processing of the data.

This constraint allows for the independent evolution of both components, offering a great deal of flexibility by letting client applications improve without affecting the server code and vice-versa.

### Stateless
The constraint to be added on top of the previous one is the stateless constraint. Communication between client and server must be stateless, meaning that each request done from the client must have all the information required for the server to understand it, without taking advantage of any stored data.

This constraint represents several improvements for the underlying architecture:

* **Visibility:** Monitoring the system becomes easy when all the information required is inside the request.

* **Scalability:** By not having to store data between requests, the server can free resources faster.

* **Reliability:** A system that is stateless can recover from a failure much easier than one that isn’t, since the only thing to recover is the application itself.

* **Easier implementation:** Writing code that doesn’t have to manage stored state data across multiple servers is much easier to do, thus the full server-side system becomes simpler.

This constrains are a trade-off. On one hand, benefits are gained by the system, but on the other side, network traffic could potentially be harmed by adding a minor overhead on every request from sending repeated state information.

### Cacheable
The cacheable constraint is added to the current set of constraints. It proposes that every response to a request must be explicitly or implicitly set as cacheable, when applicable.

By caching the responses, there are some obvious benefits that get added to the architecture. 

On the server side, some interactions are completely bypassed while the content is cached. On the client side, an apparent improvement of performance is perceived. The trade-off with this constraint is the possibility of cached data being stale, due to poor caching rules. This constraint is, again, dependent on the type of system being implemented.

### Uniform Interface
One of REST’s main characteristics and winning points when compared to other alternatives is the uniform interface constraint. 

By keeping a uniform interface between components, you simplify the job of the client when it comes to interacting with your system. 

Another major winning point here is that the client’s implementation is independent of yours, so by defining a standard and uniform interface for all of your services, you effectively simplified the implementation of independent clients by giving them a clear set of rules to follow.

Said rules are not part of the REST style, but there are constraints that can be used to create such rules for each individual case.

This benefit doesn’t come without a price, though; as with many other constraints, there is a trade-off here.

Having a standardized and uniform interface for all interactions with your system might harm performance when a more optimized form of communication exists. Particularly, the REST style is designed to be optimized for the Web, so the more you move away from that, the more inefficient the interface can be.

*Note In order to achieve the uniform interface, a new set of constraints must be added to the interface: identification of resources, manipulation of resources through representation, self-descriptive messages, and hypermedia as the engine of application state (a.k.a HATEOAS).*

### REST Layered System

REST was designed with the Internet in mind, which means that an architecture that follows REST is expected to work properly with the massive amount of traffic that exists in the web of webs.

In order to achieve this, the concept of layers is introduced. By separating components into layers, and allowing each layer to only use the one below and to communicate its output to the one above, you simplify the system’s overall complexity and keep component coupling in check.

The main disadvantage of this constraint is that for small systems, it might add unwanted latency into the overall data flow, due to the different interactions between layers.

### Code-on-Demand
Code-on-demand is the only optional constraint imposed by REST, which means that an architect using REST can choose whether or not to use this constraint, and either gains its advantages or suffers its disadvantages.

With this constraint, the client can download and execute code provided by the server (such as Java applets, JavaScript scripts, etc.). In the case of REST APIs, this constraint seems unnecessary, because the normal thing for an API client to do is just get information from an endpoint, and then process it however needed, but for other uses of REST, like web servers, a client
(i.e., a browser) will probably benefit from this constraint.

---

All of these constraints provide a set of virtual walls within which an architecture can move and still gain the benefits of the REST design style.
