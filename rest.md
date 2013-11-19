<a name="toc"></a>
#Table of Contents
- [Introduction](#introduction)
- [REST Constraints](#constraints)
- [Designing a Web-based REST API](#restapi)
-------


#Introduction
<a name="introduction"></a>

[Roy Fielding](#roy_fielding) introduced REST in his 2005 doctorate dissertation for UC Irvine.  REST is the short-form of [Representational State Transfer](#rest).  Fielding's intent was to describe an architectural approach to designing network-based systems or distributed hypermedia systems such as the World-Wide Web.

Fielding is a contributor to several core specifications that make up the Web.  He has been credited with contributions to the [HTTP/1.0](#http), [URI](#uri), and [HTTP/1.1](#http) specifications.  While REST, as an architectural style, has been applied to HTTP/1.1, the intention was not to limit the application of REST to HTTP.  As mentioned before, REST can be applied to the design of any network-based system; not just the Web.

For clarity of this guide, let's contrast what REST is against what it is not.

| REST is |  REST is not |
| :--------- | ---------------------: |
| an architectural style | extensionless URLs |
| vendor-agnostic | a feature of a framework |
| a set of constraints for network-based systems | a method for designing web applications |
| a means of transferring state via representations of resources | a religion |

Before diving into examples and detailed guidance, please checkout the [REST Constraints](#constraints).  The material is concise and foundational to the detailed materials.

-----

#REST Constraints
<a name="constraints"></a>
[Table of Contents](#toc)

REST consists of six constraints, that is to say that the following six needs must be met in order to create a system that is REST-ful.

## Client/Server
REST requires a distinction between a client system and a server system.  This establishes roles between two systems that are communicating.  The client system is responsible for initiating communication via a **Request**.  And, the server system processes the **Request** and issues a **Response**.  That concept  establishes the foundation for a communication protocol.

In addition, the **Client/Server** constraint facilitates **separation of concerns**, an architectural concept used to decouple functions into independent components.  Here, a server system will be independent of the client system and vice-versa.  The communication between them will be enabled by their adherence to a **Uniform Interface**.

## Uniform Interface
The **Uniform Interface** is a set of conventions a network-based system must follow in order to communicate with other systems across the network.  This constraint consist of four parts.

### Identification of Resources
Within the client/server paradigm, clients make requests regarding **Resources**, and servers respond in-turn.  This interaction requires systems to agee to a standard of identifying resources available on the network.  HTTP uses **Uniform Resource Identifiers** for this purpose.  

```
http://en.wikipedia.org/wiki/Uniform_resource_identifier
```

That is an example of a URI.  It consists of the following parts:

| part | value |
| :----- | :------- |
| scheme | http |
| host | en.wikipedia.org |
| path | wiki/Uniform_resource_identifier |

In addition to identifying the resource, this URI can be used to locate the resource on the Web.  

> When you are not dereferencing, you should not look at the contents of the URI string to gain other information - Tim Berners-Lee

Additional information about a resource should be shared via other means.

### Representations of Resources
Resources are not exchanged in the typical communication between a client and server.  Instead, representations of resources are exchanged.  Differences in representations can include:

* Data format e.g. XML, JSON, CSV, HTML, Plain text, JPG, etc.
* Structure of data - any combination of the individual fields that describe the resource

These representations are also described as **media types**.  Client systems should specify a media type within each request.  Likewise, server systems should specify the media type within each response.  This information helps make each communication **Self-Describing**.

### Self-Describing Messages
Any information exchanged between systems that is not proposed state of a resource or current state of a resource, is considered metadata.  Metadata helps systems determine how to handle the state that is being transferred.  

HTTP facilitates the communication of metadata via a set of uniform header fields.  See the [HTTP Guidance]() for more information on the fields.

### HATEOAS
HATEOAS, or **Hypermedia As The Engine Of Application State**, utilizes Hyperlinks and Hypertext to inform client systems of available resources.  This concept is pervasive in HTTP.  A simple request of a web page resource may return Hypertext Markup Language containing Hyperlinks to related web page resources.  This allows a client system to dynamically discover other available Resources.  In addition, context can be communicated to provide the client system with details about the relationship between the resources.


```
<a href="/some-other-document">Some other document</a>
```


The above HTML is an example of a hyperlink referencing another document.  Notice it uses a URI to identify the resource.  The text, "Some other document", offers some relative information about the resource.  Similarly, a search result resource may offer the following as a hyperlink:


```
<a href="/search?q=representational+state+transfer&start=10" rel="next">Page 2</a>
```


A client system receiving this hyperlink would have enough information to request "Page 2" of this search result.  

Keep in mind, the above examples are using HTML as the data format being transferred.  This HTML provides:

* the identity of related resources
* and, context about how the resource is related

Therefore, HATEOAS is not limited to HTML.  The same result can be achieved in other data formats, such as CSV:

"href", "description", "rel"
"/search?q=REST&start=10", "next page", "next"

Rendered in a spreadsheet application: 

| href | description | rel |
| :----- | :--------------- | :--- |
| /search?q=REST&start=10 | next page | next |

##Layered System

In order to accommodate specialized needs such as caching, security, and load-balancing, a REST system must allow **transparent** systems to exist between the client system and the server system.  This constraint allows the use of **proxies** and **gateways** where proxies are client-side intermediaries and gateways are server-side intermediaries.

##Cacheability

As a means of reducing network traffic and client-perceived latency, server systems should specify the cacheability of each response.  

Again, HTTP offers a few uniform fields for specifying cacheability of a response.  This is further discussed in the HTTP guidance.

##Stateless

This constraint goes hand-and-hand with the **Self-Described Messages** constraint.  Essentially, a REST system cannot guarantee session state will be maintained by the server.  Therefore, clients must assume responsibility for maintaining session state.  A request made to a server must include all information necessary for the server to process it.

This allows servers to process requests for a larger number of clients.  Therefore, this constraint allows for better scale of REST-ful systems.

##Code-on-Demand

This is the only optional constraint.  Code-on-demand allows a client to request an executable program from the server.  Typically, these programs are in the form of Flash, Java applets, or, more commonly, Javascript.  The key to this constraint is that the client must be able to understand and execute the program sent by the server.

------

[Table of Contents](#toc)
#Developing a Web-based REST API
<a name="restapi"></a>

##What does it mean for a Web-based API to be REST-ful?

Understanding REST as an architectural style is paramount to defining an API as REST-ful.  As discussed earlier, REST is an approach that was applied to the design of HTTP, which means REST constraints are foundational to the way the the Web works.

In this section, general design guidance will be provided.  This guidance will provide insight into how to use HTTP and general practices to:

* Model resources
* Work with common media types
* Limit response data, or provide partial representations
* Incorporate hypermedia
* Use HTTP verbs to determine how to process a request
* Version your API
* Page large amounts of response data
* Use HTTP status codes
* Specify cacheability
* Authenticate clients

##Modeling Resources and designing URIs

Resources consist of four archetypes:

* Collections
* Stores
* Documents
* and, Controllers

A document is the base archetype.  It is the type used to interact with a singular resource.

```http
http://www.myblog.com/posts/how-to-be-awesome
```

The above example is a URI to a single blog post presumably identified by the last segment, **"how-to-be-awesome"**.

Collections and stores behave like directories of resources, or logical groupings.  Collections are managed by the server, which means documents added to a collection are given an identity by the server.  Whereas, stores are managed by the client.  A client can put a document into a store with an identifier.

An example URI in a collection may look like this:

```http
https://corpintranet/projects
```

The last segment ```/projects``` is the identifier of the collection.

A ```POST``` to add a resource to this collection could look like this:

```http
POST /projects HTTP/1.1
HOST: corpintranet
Content-Type: application/json
{
	"name": "My New Project",
	"Owner": "John Doe"
}
```

Once processed, the new resource would be accessible at a URI created by the server:

```http
https://corpintranet/projects/1
```

Here, **"1"** is the identifier of the newly created document resource.

Similarly, an example URI in a store may look like this:

```http
https://corpintranet/teams
```

A new resource could be added to the store like this:

```http
POST /teams HTTP/1.1
HOST: corpintranet
Content-Type: application/json
{
	"id": "na-sales-team",
	"name": "North American Sales Team"
}
```

Which would yield a client-specified URI for the new resource:

```http
https://corpintranet/teams/na-sales-team
```

Lastly, controllers are application-specific actions that cannot be logically mapped via other HTTP constructs.  

Here is an example URI from Github that models an action:

```http
https://github.com/PF-iPaaS/core-services/compare/master...oauth-refresh
```

```compare``` is the controller resource.  ```master...oauth-refresh``` is a query parameter in this example.  

###In Practice
1. Collections and Stores should use plural nouns as identifiers
2. Documents should use a singular noun or unique identifier
3. Controllers should use a verb or verb phrase as the identifier
4. URIs should not indicate CRUD functions e.g. ```/getUsers```

##Media Types

working on this...