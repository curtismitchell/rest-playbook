# REST Basics

## Resources

Representational State Transfer is centered around interactions with **representations** of resources.  Resources are typically entities expressed as nouns within a system.  A resource could be a virtual entity like a BitCoin.  Or, a resource could be a physical entity like a printer.

>A flesh-and-blood or bricks-and-mortar resource becomes a web resource by the simple act of making the information associated with it accessible on the Web. - REST in Practice, Jim Webber, Savas Parastatidis, and Ian Robinson. 

By definition, a representation is simply a portrayal that is designed with the expressed intent of allowing interaction and/or manipulation of a resource.

##Constraints

REST consists of six constraints, that is to say that the following six rules must apply in order to create a "RESTful" system.

##Client/Server
REST requires a distinction between a client system and a server system.  This establishes roles between two systems that are communicating.  The client system is responsible for initiating communication via a **Request**.  And, the server system processes the **Request** and issues a **Response**.  That concept  establishes the foundation for a communication protocol.

In addition, the **Client/Server** constraint facilitates **separation of concerns**, an architectural concept used to decouple functions into independent components.  Here, a server system will be independent of the client system and vice-versa.  The communication between them will be enabled by their adherence to a **Uniform Interface**.

##Uniform Interface
The **Uniform Interface** is a set of conventions a network-based system must follow in order to communicate with other systems across the network.  This constraint consist of four parts.

###Identification of Resources
Within the client/server paradigm, clients make requests regarding **Resources**, and servers respond in-turn.  This interaction requires systems to agee to a standard of identifying resources available on the network.  HTTP uses **Uniform Resource Identifiers** for this purpose.  

```http
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


<pre>
&lt;<span class="text-danger">a</span> <span class="text-info">href</span>="/some-other-document"&gt;Some other document&lt;/<span class="text-danger">a</span>&gt;
</pre>


The above HTML is an example of a hyperlink referencing another document.  Notice it uses a URI to identify the resource.  The text, "Some other document", offers some relative information about the resource.  Similarly, a search result resource may offer the following as a hyperlink:


<pre>
&lt;<span class="text-danger">a</span> <span class="text-info">href</span>="/search?q=representational+state+transfer&start=10" rel="next"&gt;Page 2&lt;/<span class="text-danger">a</span>&gt;
</pre>

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

<a class="btn btn-primary" href="developing-api.md#What_does_it_mean_for_a_Web-based_API_to_be_REST-ful?">Next: Developing an API</a>