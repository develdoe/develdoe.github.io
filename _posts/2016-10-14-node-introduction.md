---
date: '2016-10-14 14:30 +0200'
published: true
title: Node Introduction
category:
  - Node
---
Node.js is an open source, cross-platform runtime environment for developing server-side and networking applications.

Node.js has an event-driven architecture capable of asynchronous I/O. These design choices aim to optimize throughput and scalability in Web applications with many input/output operations, as well as for real-time Web applications

Aalthough Node.js is not a JavaScript framework, many of its basic modules are written in JavaScript, and developers can write new modules in JavaScript. The runtime environment interprets JavaScript using Google's V8 JavaScript engine.


## Overview

Node.js allows the creation of Web servers and networking tools using JavaScript.

Node.js also have a collection of "modules" that handle various core functionality. Modules are provided for file system I/O, networking (DNS, HTTP, TCP, TLS/SSL, or UDP), binary data (buffers), cryptography functions, data streams and other core functions. Node.js's modules use an API designed to reduce the complexity of writing server applications.

Node.js is primarily used to build network programs such as Web servers. The biggest difference between Node.js and other technologies is that most functions in other technologies block until completion (commands execute only after previous commands have completed), while functions in Node.js are designed to be non-blocking (commands execute in parallel, and use callbacks to signal completion or failure).

### Platform architecture

Node.js brings event-driven programming to web servers, enabling development of fast web servers in JavaScript. 

Developers can create highly scalable servers without using threading, by using a simplified model of event-driven programming that uses callbacks to signal the completion of a task. Node.js was created because concurrency is difficult in many server-side programming languages, and often leads to poor performance. Node.js connects the ease of a scripting language (JavaScript) with the power of Unix network programming.

Node.js was built on the Google V8 JavaScript engine since it was open-source under the BSD license, extremely fast, and proficient with internet fundamentals like HTTP, DNS, TCP. Also, JavaScript was a well-known language, making Node.js immediately accessible to the entire web development community.

### Industry support

Programmers have built thousands of open-source libraries for Node.js - most of them hosted on the npm website. 

The Node.js developer community has two main mailing lists and the IRC channel #node.js on freenode. There is an annual Node.js developer conference, called NodeConf. The open-source community has developed server frameworks to accelerate the development of applications. Such frameworks include Connect, Express.js, Socket.IO, Koa.js, Hapi.js, Sails, Meteor, Derby, and many others.

Modern desktop IDEs provide editing and debugging features specifically for Node.js applications. 

## Technical details

Node.js is a Javascript runtime environment that processes incoming requests in a loop, called the event loop.

### Threading

Node.js operates on a single thread, using non-blocking I/O calls, allowing it to support tens of thousands of concurrent connections without incurring the cost of thread context switching. 

The design of sharing a single thread between all the requests that uses the observer pattern is intended for building highly concurrent applications, where any function performing I/O must use a callback. In order to accommodate the single-threaded event loop, Node.js utilizes the libuv library that in turn uses a fixed-sized threadpool that is responsible for some of the non-blocking asynchronous I/O operations.

A downside of this single-threaded approach is that Node.js doesn't allow vertical scaling by increasing the number of CPU cores of the machine it is running on without using an additional module, such as cluster, StrongLoop Process Manager or pm2. However, developers can increase the default number of threads in the libuv threadpool; these threads are likely to be distributed across multiple cores by the server operating system.

Execution of parallel tasks in Node.js is handled by a thread pool. The main thread-call, post tasks to the shared task queue, that then threads in the thread pool and executes. 

Inherently non-blocking system functions, like networking, translates to kernel-side non-blocking sockets. 

While inherently blocking system functions like file I/O run in a blocking way on its own thread. 

When a thread in the thread pool completes a task, it informs the main thread of this that in turn wakes up and execute the registered callback. Since callbacks are handled in serial on the main thread, long lasting computations and other CPU-bound tasks will freeze the entire event-loop until completion.

### V8

V8 is the JavaScript execution engine built for Google Chrome and open-sourced by Google in 2008. 

Written in C++, V8 compiles JavaScript source code to native machine code instead of interpreting it in real time. 

Node.js uses libuv to handle asynchronous events. Libuv is an abstraction layer for network and file system functionality on both Windows and POSIX-based systems like Linux, Mac OS X, OSS on NonStop and Unix.

The core functionality of Node.js resides in a JavaScript library. The Node.js bindings, written in C++, connect these technologies to each other and to the operating system.

### Package management

npm is the pre-installed package manager for the Node.js server platform. 

It is used to install Node.js programs from the npm registry, organizing the installation and management of third-party Node.js programs. npm is not to be confused with the CommonJS require() statement. It is not used to load code; instead, it is used to install code and manage code dependencies from the command line.

### Unified API

Node.js can be combined with a browser, a document database (such as MongoDB or CouchDB) and JSON for a unified JavaScript development stack. 

With the adaptation of what were essentially server-side development patterns like MVC, MVP, MVVM, etc., Node.js allows the reuse of the same model and service interface between client-side and server-side.

### Event loop

Node.js registers itself with the operating system so that it is notified when a connection is made, and the operating system will issue a callback. 

Within the Node.js runtime, each connection is a small heap allocation. Traditionally, relatively heavyweight OS processes or threads handled each connection. Node.js uses an event loop for scalability, instead of processes or threads. 

In contrast to other event-driven servers, Node.js's event loop does not need to be called explicitly. Instead callbacks are defined, and the server automatically enters the event loop at the end of the callback definition. Node.js exits the event loop when there are no further callbacks to be performed.

