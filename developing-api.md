#Developing a Web-based REST API

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
5. URIs should not indicate the format of data e.g. ```/users.xml```

##Media Types

Media Types are the representations of resources.  Therefore, they are at the core of **Representational** State Transfer.  They provide structure to the data that is accessible from a resource.

###Syntax
Media Type declarations use the following syntax:

<pre>{type}/{subtype}(optionally);{parameter(s)}</pre>

Here is an example:

<pre>text/plain;charset=utf-8</pre>

The <pre>;charset=utf-8</pre> part is optional.  

As mentioned in the [constraints](constraints.md) section, media types are a combination of the data format and the fields.  HTTP allows media types to be specified in the ```Accept``` and ```Content-type``` header fields within a request.

```Accept``` tells the server which data format the client would like to receive in the response.  While, ```Content-Type``` tells the recipient how the body of the message is formatted.  It applies to both requests and responses.

As an example, consider a fictitious ```/car``` resource.  Assume the fields consist of make, model, and year.  The following table illustrates sample requests and responses for the given media type:

| VERB | URI | ACCEPT | Response Body |
| :--- | :-- | :----- | :------------ |
| GET | /car | application/json | ```{	make: "Honda",	model: "Accord", year: "2013" }``` |
| GET | /car | application/xml | ``` <car><make>Honda</make><model>Accord</model><year>2013</year></car>``` |
| GET | /car | text/plain | make=honda&model=accord&year=2013 |

In the above table, the difference in each response is the data format.  This assumes the structure of the data remains constant.  For scenarios where the structure of the data may differ, *vendor-specific* media types may be used.  

Consider the following requests:

| VERB | URI | ACCEPT | Response Body |
| :--- | :-- | :----- | :------------ |
| GET | /car | application/vnd.acmecorp.product1.car+json | ```{	make: "Honda",	model: "Accord", year: "2013" }``` |
| GET | /car | application/vnd.acmecorp.product2.car+json | ```{	manufacturer: "Honda", type: "Sedan" }``` |

In this case, Acme Corp has two different structures for the Car resource.  By using a vendor-specific media type, a client is able to request the exact structure it wants.  

Keep in mind, the goal is to maintain uniformity in the communications between client and server, while simultaneously maintaining separation of concerns.  Media types like ```application/json```, ```application/xml```, ```text/plain```, ```image/jpg```, and others are considered to be **well-known**.  These media types are registered with the IANA, Internet Assigned Numbers Authority.  And, they are so commonly used, that it is safe to presume all clients and servers know about them.

On the other hand, vendor-specific media types are not well known.  Therefore, clients may not know how to handle them.  Documentation is a great way to share the details of custom media types with developers.  Another approach is to use hypermedia and code-on-demand to enable client applications to discover how to process vendor-specific media types.

###Versioning

One API feature that media types can provide is versioning.  Being an online entity, a Web API will very likely evolve over time.  New resources may be added while deprecated resources may be retired.  Perhaps more frequently, data structures and formats will change.  To accommodate this, a version can be passed as a parameter with a media type.

```http
Accept: application/vnd.acmecorp.product1.car+json;version2
```

Using the ```Accept``` header, a client can request a specific version of a vendor-specified media type.  This is helpful if Acme Corp introduces changes to the media type that are not compatible - likely due to a change in required fields.

###In Practice

1. Take advantage of the ```Accept``` header when multiple representations of a resource are available.
2. Optionally, allow clients to specify an ```ACCEPT``` value via query parameter.  e.g. ```http://api.acmecorp.com/car?accept=application/csv```
3. Take advantage of the optional parameters for features like versioning.


