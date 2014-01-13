#Rules for developing an API

##URI

1. A `URI` should uniquely identify one resource
2. A resource can be uniquely identified by more than one `URI`

## Resource Modeling

1. `Collection Resources` and `Store Resources` should be plural nouns
2. `Controller Resources` should be verbs
3. Prefer a `URI` to be just long enough to uniquely identify the resource
4. Prefer `media types` to indicate data format instead of the `URI`
5. Use course-grain nouns to define broad resources versus fine-grain nouns such as those found in an object hierarchy

##HTTP Verbs

1. A `GET` request on a `Collection` should return a list
2. A `POST` request on a `Collection` should be used to add a new resource and return the resource's `URI` in the `Location` header
3. A `PUT` request on a `Collection` should not be supported (see `Store`)
4. A `DELETE` request on a `Collection` should erase all resources in the collection
5. A `GET` request on a `Document` in a `Collection` should return the single resource if it exists, a `404` if it does not
6. A `POST` request on a `Document` in a `Collection` or a `Store` should update some or all of the fields in the document
7. A `PUT` request on a `Document` in a `Collection` should return a `400`
8. A `DELETE` request on a `Document` in a `Collection` or a `Store` should erase the document
9. A `GET` request on a `Store` should return a list of resources
10. A `POST` request on a `Store` should add a resource to the store and return the resource's `URI` in the `Location` header
11. A `PUT` request on a `Store` should replace the list of resources
12. A `DELETE` request on a `Store` should erase all resources in the list
13. A `GET` request on a `Document` in a `Store` should return the document 
14. A `PUT` request on a `Document` in a `Store` should replace the document if it exists, otherwise create a new document using the given URI
15. A `POST` request is the only method supported for `Controller` resources
16. A `HEAD` request should return the same headers of a `GET` request, but the response should not include a body
17. A `OPTIONS` request should return an `Accept` header containing the methods that could be used on the resource

##Media Types

1. `Media Types` should be used to specify the representation of a resource
2. The `Media Types` should be requested via the `Accept` header and communicated in the response via the `Content-Type` header
3. Prefer use of the `Accept` header to request a `Media Type`, but, optionally, allow an `Accept` parameter in the query string
4. Dates should adhere to the [ISO 8601](http://www.w3.org/TR/NOTE-datetime) standard

##Versioning

1. Prefer to enforce versioning via the `Media Types` instead of the `URI`

##Partial Representations

1. When a partial representation of a resource is consistent across many clients, create a `Media Type` that can be requested by the clients
2. When a partial set of fields are arbitrarily selected by the clients, use a documented parameter to receive a list of fields from the client

##Filtering and Pagination

1. Use the query portion of the URI to enforce filtering and pagination
2. Use parameters that are documented and consistent across `Store` and `Collection` resources for pagination
3. Use `Media Type` documentation to indicate parameter names that can be used to filter each `Store` and `Collection`

##Hypermedia (HATEOAS)

1. Use `Hypermedia` to represent related resources
2. Minimally, include the `URI` and `REL`, or **Link Relation** in the representation of `Hypermedia`
3. For data formats that (unlike HTML) do not have a common representation of `Hypermedia`, use a documented representation that is consistent across all resources
4. Use well-known [Link Relations](http://www.iana.org/assignments/link-relations/link-relations.xml) when possible
5. When possible, use `Hypermedia` to inform the client of available endpoints

##Caching

1. Always specify the cacheability of a request using response headers
2. When the `Cache-Control` header in the request is `no-cache`, provide a fresh representation
3. Use the `Expires` header to indicate when a representation should be considered stale
4. Prefer use of the `ETag` header over the `Last-Modified` header as an indicator of whether to serve a fresh representation

##Status Codes

1. Use `200` level status codes to indicate success
2. Use `300` level status codes to indicate redirection (using the `location` header)
3. Use `400` level status codes to indicate an issue in the request from the client
4. Use `500` level status codes to indicate an issue on the server

##Authentication and Authorization

1. Use [TLS]() to secure communication of sensitive data
2. Prefer [OAuth 2](developing-api.md#authentication-and-authorization) protocol for authorizing requests
3. Use [TLS]() and [Basic Authentication]() to authenticate a client that is exchanging an [Authorization Code]() for an [Access Token]()
4. Use the `Authorization` header to receive [Access Tokens]() from client requests
5. Consider the **Client Type** and trust level when determining the **scope** of access allowed via an [Access Token]()  

