---
date: '2016-09-28 20:37 +0200'
published: true
title: REST Resources
category:
  - Development
---
The main building blocks of a REST architecture are the resources. Anything that can be named can be a resource (a web page, an image, a person, a weather service report, etc.). 

Resources define what the services are going to be about, the type of information that is going to be transferred, and their related actions. The resource is the main entity from which everything else is born.

A resource is the abstraction of anything that can be conceptualized (from an image file, to a plain text document). The structure of a resource is shown in the table below:

|Property|Description|
|:-------|----------:|
|Representations|It can be any way of representing data (binary, JSON, XML, etc.). A single resource can have multiple representations.|
|Identifier|A URL that retrieves only one specific resource at any given time.|
|Metadata|Content-type, last-modified time, and so forth.|
|Control data|Is-modifiable-since, cache-control.|

### Representations
At its core, a representation is a set of bytes, and some metadata that describes these bytes. A single resource can have more than one representation; just think of a weather service report. The weather report for a single day could potentially return the following information:

* The date the report is referencing
* The maximum temperature for the day
* The minimum temperature for the day
* The temperature unit to be used
* A humidity percentage
* A code indicating how cloudy the day will be (e.g., high, medium, low)

Now that the resource’s structure is defined, here are a few possible representations of the same resource:

```js
JSON {
  "date": "2014-10-25",
  "max_temp": 25.5,
  "min_temp": 10.0,
  "temp_unit": "C",
  "humidity_percentage": 75.0,
  "cloud_coverage": "low"
}
```

```xml
XML
<?xml version='1.0' encoding='UTF-8' ?>
<root>
  <temp_unit value="C" />
  <humidity_percentage value="75.0" />
  <cloud_coverage value="low" />
  <date value="2014-10-25" />
  <min_temp value="10.0" />
  <max_temp value="25.5" />
</root>
```

```bash
2014-10-25|25.5|10.0|C|75.0|low
```
And there could be many more. They all successfully represent the resource correctly; it is up to the client to read and parse the information. Even when the resource has more than one representation, it is common for clients to only request one of them. Unless you’re doing some sort of consistency check against the API, there is no point in requesting more than one representation of the same resource, is there?

There are two very popular ways to let the client request a specific representation on a resource that has more than one. The first one directly follows the principles described by REST, called content negotiation, which is part of the HTTP standard. The second one is a simplified version of this, with limited benefits. For the sake of completeness, I’ll quickly go over them both.

#### Content Negotiation
As mentioned, this methodology is part of the HTTP standard so it’s the preferred way according to REST (at least when focused on API development on top of HTTP). It is also more flexible and provides further advantages than the other method.

It consists of the client sending a specific header with the information of the different content types (or types of representations) supported, with an optional indicator of how much preferred that format is. Let’s look at an example from the “Content Negotiation” page on Wikipedia:

```
Accept: text/html; q=1.0, text/*; q=0.8, image/gif; q=0.6, image/jpeg; q=0.6, image/*;
q=0.5, */*; q=0.1
```

This example is from a browser configured to accept various types of resources, but preferring HTML over plain text, and GIF or JPEG images over other types, but ultimately accepts any other content type as a last resort.

On the server side, the API is in charge of reading this header and finding the best representation for each resource, based on the client’s preferences.


#### Using File Extensions
Even though this approach is not part of the REST proposed style, it is widely used and a fairly simple alternative to the somewhat more complex other option.

During the last few years, using file extensions has become an alternative preferred over using content negotiation; it is a simpler version and it doesn’t rely on a header being sent, but instead, it works with the concept of file extensions. The extension portion of the file’s name indicates the content type to the operating system and any other software trying to use it; so in the following case, the extension added to the resource’s URL (unique identifier) indicates to the server the type of representation wanted.

````
GET /api/v1/books.json 
GET /api/v1/books.xml
```

### Resource Identifier
The resource identifier should provide a unique way of identification at any given moment and it should provide the full path to the resource. The way to access the resource it to provide its full URI (unique resource identifier).

The identifier of each resource must be able to reference it unequivocally at any given moment in time. This is an important distinction, because a URI like the following might reference Harry Potter and the Half Blood Prince for a certain period of time, and then Harry Potter and the Deathly Hollows one year later:

```
GET /api/v1/books/last
```

This renders that URI as an invalid resource ID. Instead, each book needs a unique URI that is certain to not change over time:

```
GET /api/v1/books/j-k-rowling/harry-potter-and-the-deathly-hollows
GET /api/v1/books/j-k-rowling/harry-potter-and-the-half-blood-prince
```

A valid example for getting the last book might be:

```
GET /api/v1/books?limit=1&sort=created_at
```

The preceding URI references the lists of books, and it asks for only one, sorted by its publish date, thus rendering the last book added.

### Actions

Identifying a resource is easy. You know how to access it and you even know how to request for a specific format (if there is more than one). But that’s not all that REST proposes. Since REST is using the HTTP protocol as a standing point, the latter provides a set of verbs that can be used to reference the type of action being done over a resource.

There are other actions, aside from accessing, that a client app can take in the resources provided by an API; these depend on the service provided by the API. These actions could potentially be anything, just like the type of resources handled by the system. Still, there is a set of common actions that any system that is resource-oriented should be able to provide: CRUD (create, retrieve, update, and delete) actions.
These so-called actions can be directly mapped to the HTTP verbs, but REST does not enforce a standardized way to do so. However, there are some actions that are naturally derived by the verb and others that have been standardized by the API development community over the years, as shown in the table bolow: 

|HTTP Verb|Proposed Action|
|:--------|--------------:|
|GET|Access a resource in a read-only mode.|
|POST|Normally used to send a new resource into the server (create action).|
|PUT|Normally used to update a given resource (update action).|
|DELETE|Used to delete a resource.|
|HEAD|Not part of the CRUD actions, but the verb is used to ask if a given resource exists without returning any of its representations.|
|OPTIONS|Not part of the CRUD actions, but used to retrieve a list of available verbs on a given resource (i.e., What can the client do with a specific resource?).|

That said, a client may or may not support all of these actions. It depends on what needs to be achieved. For instance, web browsers— a clear and common example of a REST client— only have support for
GET and POST verbs from within the HTML code of a page, such as links and forms (although using the XMLHTTPRequest object from JavaScript would provide support for the major verbs mentioned earlier).

**Note: The list of verbs and their corresponding actions are suggestions. For instance, there are some developers who prefer to switch PUT and POST, by having PUT add new elements and POST update them.**

#### Complex Actions

CRUD actions are normally required, but they’re just a very small subset of the entire spectrum of actions that a client can do with a specific resource or set of resources.
For instance, take common actions like searching, filtering, working with subresources, sharing a blogpost, and so forth. All of these actions fail to directly match one of the verbs that I mentioned.
The first solution that many developers succumb to is to specify the action taken as part of the URL; so you might end up with things like the following:

```
GET /api/v1/blogpost/12342/like 
GET /api/v1/books/search
GET /api/v1/authors/filtering
```

hose URLs break the URI principle, because they’re not referencing a unique resource at any given time; instead, they’re referencing an action on a resource (or group of resources). They might seem like a good idea at first, but in the long run, and if the system keeps on growing, there will be too many URLs, which will increase the complexity of the client using the API.
So to keep things simple, use the following rule of thumb: Hide the complexity of your actions behind the ? sign. This rule can apply to all verbs, not just GET, and can help achieve complex actions without compromising the URL complexity of the API. For the preceding examples, the URIs could become something like this:

```
PUT /api/v1/blogposts/12342?action=like
GET /api/v1/books?q=[SEARCH-TERM]
GET /api/v1/authors?filters=[COMMA SEPARATED LIST OF FILTERS]
```

Notice how the first one changed from a GET to a PUT due to the fact that the action is updating a resource by liking it.

### Hypermedia Respons and Main Entry Point

To make REST’s interface uniform, several constraints must be applied. One of them is Hypermedia as the Engine of Application State, also known as HATEOAS. If you follow the rules of REST you end up with a great new feature that allows any RESTful system client to start the interaction knowing only a single endpoint of the entire system, the root endpoint.

Example of a JSONResponse from the Root Endpoint

```json
{
  "metadata": {
    "links": [
      "books": {
        "uri": "/books",
        "content-type": "application/json"
      },
      "authors": {
        "uri": "/authors",
        "content-type": "application/json"
      }
] }
}
```

Example of Another JSON Response with Hyper links to Other Resources
```JS
{
    "resources": [{
        "title": "Harry Potter and the Half Blood prince",
        "description": "......",
        "author": {
            "name": "J.K.Rowling",
            "metadata": {
                "links": {
                    "data": {
                        "uri": "/authors/j-k-rowling",
                        "content-type": "application/json"
                    },
                    "books": {
                        "uri": "/authors/j-k-rowling/books",
                        "content-type": "application/json"
                    }
                }
            }
        },
        "copies": 10
    }, {
        "title": "Dune",
        "description": "......",
        "author": {
            "name": "Frank Herbert",
            "metadata": {
                "links": {
                    "data": {
                        "uri": "/authors/frank-herbert",
                        "content-type": "application/json"
                    },
                    "books": {
                        "uri": "/authors/frank-herbert/books",
                        "content-type": "application/json"
                    }
                }
            }
        },
        "copies": 5
    }],
    "total": 100,
    "metadata": {
        "links": {
            "next": {
                "uri": "/books?page=1",
                "content-type": "application/json"
            }
        }
    }
}
```

With that information, the client application knows the following logical steps:

1. How to get the information from the books authors
2. How to get the list of books by the authors
3. How to get the next page of results

Note that the full list of authors is not accessible through this endpoint; this is because it’s not needed in this particular use case, so the API just doesn’t return it. It was present on the root endpoint, though; so if the client needs it when displaying the information to the end user, it should still be available.

Each link from the preceding example contains an attribute specifying the content-type of the representation of that resource. If the resources have more than one possible representation, the different formats could be added as different links inside each resource’s metadata element, letting the client choose the most adequate to the current use case, or the type could change based on the client’s preferences (content negotiation).

Note that the earlier JSON structure (more specifically, the metadata elements’ structure) is not important. The relevant part of the example is the information presented in the response. Each server has the freedom to design the structure as needed.

Not having a standard structure might harm the developer experience while interacting with your system, so it might be a good idea to adopt one. This is certainly not enforced by REST, but it would be a major point in favor of your system. A good standard to adopt in this case would be Hypertext Application Language, or HAL,6 which tries to create a standard for both XML and JSON when representing resources with those languages.


#### A Few Notes on HAL

HAL tries to define a representation as having two major elements: resources and links.
According to HAL, a resource has links, embedded resources (other resources associated to their parent), and a state (the actual properties that describe the resource). On the other hand, links have a target (the URI),
a relation, and some other optional properties to deal with deprecation, content negotiation, and so forth. below shows the preceding example represented using the HAL format:

```json
{
    "_embedded": [{
        "title": "Harry Potter and the Half Blood prince",
        "description": "......",
        "copies": 10,
        "_embedded": {
            "author": {
                "name": "J.K.Rowling",
                "_links": {
                    "self": {
                        "href": "/authors/j-k-rowling",
                        "type": "application/json+hal"
                    },
                    "books": {
                        "href": "/authors/j-k-rowling/books",
                        "type": "application/json+hal"
                    }
                }
            }
        }
    }, {
        "title": "Dune",
        "description": "......",
        "copies": 5,
        "_embedded": {
            "author": {
                "name": "Frank Herbert",
                "_links": {
                    "self": {
                        "href": "/authors/frank-herbert",
                        "type": "application/json+hal"
                    },
                    "books": {
                        "href": "/authors/frank-herbert/books",
                        "type": "application/json+hal"
                    }
                }
            }
        }
    }],
    "total": 100,
    "_links": {
        "self": {
            "href": "/books",
            "type": "application/json+hal"
        },
        "next": {
            "href": "/books?page=1",
            "type": "application/json+hal"
        }
    }
}
```
The main change in above is that the actual books have been moved inside an element called "_embedded", as the standard dictates, since they’re actual embedded documents inside the represented resource, which is the list of books (the only property that belongs to the resource is "total", representing the total number of results). The same can be said for the authors, now inside the "_embedded" element of each book.


### Status Codes
Another interesting standard that REST can benefit from when based on HTTP is the usage of HTTP status codes.7 A status code is a number that summarizes the response associated to it. There are some common ones,

like 404 for “Page not found,” or 200 for “OK,” or the 500 for “Internal server error”.
A status code is helpful for clients to begin interpreting the response, but in most cases, it shouldn’t be a substitute for it. As the API owner, you can’t really transmit in the response what exactly caused a crash on your side by just replying with the number 500. There are some cases, though, when a number is enough, like 404; although a good response will always return information that should help the client solve the problem (with a 404, a link to the home page or the root URL are good places to start).

These codes are grouped in five sets, based on their meaning:

* 1xx: Informational and only defined under HTTP 1.1.
* 2xx: The request went OK, here’s your content.
* 3xx: The resource was moved somehow to somewhere.
* 4xx: The source of the request did something wrong.
* 5xx: The server crashed due to some error on its code.

|Status Code|Meaning|
|:----------|------:|
|200|OK. The request went fine and the content requested was returned. This is normally used on GET requests.|
|201|Created. The resource was created and the server has acknowledged it. It could be useful on responses to POST or PUT requests. Additionally, the new resource could be returned as part of the response body.|
|204|No content. The action was successful but there is no content returned. Useful for actions that do not require a response body, such as a DELETE action.|
|301|Moved permanently. This resource was moved to another location and the location is returned. This header is especially useful when URLs change over time (maybe due to a change on version, a migration, or some other disruptive change), keeping the old ones and returning a redirection to the new location allows old clients to update their references in their own time.|
|400|Bad request. The request issued has problems (might be lacking some required parameters, for example). A good addition to a 400 response might be an error message that a developer can use to fix the request.|
|401|Unauthorized. Especially useful for authentication when the requested resource is not accessible to the user owning the request.|
|403| Forbidden. The resource is not accessible, but unlike 401, authentication will not affect the response.|
|404|Not found. The URL provided does not identify any resource. A good addition to this response could be a set of valid URLs that the client can use to get back on track (root URL, previous URL used, etc.).|
|405| Method not allowed. The HTTP verb used on a resource is not allowed. For instance doing a PUT on a resource that is read-only.|
|500|Internal server error. A generic error code when an unexpected condition is met and the server crashes. Normally, this response is accompanied by an error message explaining what went wrong.|

**To see the full list of HTTP status codes and their meaning, please refer to the RFC of HTTP 1.1**


### REST vs. the Past
Before REST was all cool and hip, and every business out there wanted to provide their clients with a RESTful API in their service, there were other options for developers who wanted to interconnect systems. These are still being used on old services or by services that required their specific features, but less and less so every year.

Back in the 1990s, the software industry started to think about system interoperability and how two (or more) computers could achieve it. Some solutions were born, such as COM,9 created by Microsoft, and CORBA,10 created by the Object Management Group. These were the first two implementations back then, but they had a major issue: they were not compatible with each other.

Other solutions arose, like RMI, but it was meant specifically for Java, which meant it was technology dependent, and hadn’t really caught up with the development community.

By 1997, Microsoft decided to research solutions that would use XML as the main transport language and would allow systems to interconnect using RPC (Remote Procedure Call) over HTTP, thus achieving a somewhat technology-independent solution that would considerably simplify system interconnectivity. That research gave birth to XML-RPC around 1998.

It is quite clear that the messages (both requests and responses) were overly verbose, something that was directly related to the use of XML. There are implementations of XML-RPC that exist today for several operating systems and programming languages, like Apache XML-RPC12 (written in Java), XMLRPC-EPI13 (written in C), and XML-RPC-C14 for C and C++.

After XML-RPC became more popular, it mutated into SOAP,15 a more standardized and formalized version of the same principle. SOAP still uses XML as the transport language, but the message format is now richer (and therefore complex). SOAP services are actually dependent on another technology called Web Service Description Language (WSDL). An XML-based language, it describes the services provided to clients that want to consume them.

The main drawback of these types of services was the amount of information used, both to describe them and to use them. Even though XML provided the much-required technology agnostic means of encoding data to be transmitted between two systems, it also blotted the message sent quite noticeably.
Both of these technologies (XML-RPC and SOAP + WSDL) provided the solution to system interconnectivity at a time when it was required. They provided a way to transmit messages using a “universal” language between all systems, but they also had several major issues compared to today’s leading standard. This can be clearly seen, for example, in the way developers feel about using XML instead of JSON.

|XML-RCP / SOAP|REST|
|:---|--:|
|Specific SOAP clients had to be created for each programming language. Even though XML was universal, a new client would have to be coded to parse the WSDL to understand how the service worked.|REST is completely technology agnostic and doesn’t require special clients, only a programming language capable of connectivity through the chosen protocol (e.g., HTTP, FTP, etc.).|
|The client needs to know everything about the service before initiating the interaction (thus the WSDL mentioned earlier).|The client only needs to know the main root endpoint, and with the hypermedia provided on the response, self-discovery is possible.|
|Because the service was used from within the client source code and called a specific function or method from within the server’s code, the coupling between those two systems was too big. A rewrite of the server code would probably lead to a rewrite on the client’s code.|The interface is implementation independent; the complete server-side code can be rewritten and the API’s interface will not have to be changed.|

*Comparing XML-RPC/SOAP with REST might not be entirely fair (or possible) due to the fact that the first two are protocols, whereas the latter is an architectural style; but some points can still be compared if you keep that distinction in mind.*

### Summary
This chapter was a small overview of what REST is meant to be and what kind of benefits a system will gain by following the REST style. The chapter also covered a few extra principles, like HTTP verbs and status codes, which although are not part of the REST style, are indeed part of the HTTP standard, the protocol we’re basing this book on.

Finally, The main technologies used prior to REST, and you saw how they compared to the current leading industry standard.