#Introducing REST

[Roy Fielding]() introduced REST in his 2005 doctorate dissertation for UC Irvine.  REST is the short-form of [Representational State Transfer]().  Fielding's intent was to describe an architectural approach to designing network-based systems or distributed hypermedia systems such as the World-Wide Web.

Fielding is a contributor to several core specifications that make up the Web.  He has been credited with contributions to the [HTTP/1.0](), [URI](), and [HTTP/1.1]() specifications.  While REST, as an architectural style, has been applied to HTTP/1.1, the intention was not to limit the application of REST to HTTP.  As mentioned before, REST can be applied to the design of any network-based system; not just the Web.

For clarity of this guide, let's contrast what REST is against what it is not.

| REST is |  REST is not |
| :--------- | ---------------------: |
| an architectural style | extensionless URLs |
| vendor-agnostic | a feature of a framework |
| a set of constraints to aid the design of network-based systems | a method for designing web applications |
| a means of transferring state via representations of resources | a religion |
| applied to the design of HTTP/1.1 | HTTP |

Before diving into examples and detailed guidance, please checkout the [REST Basics](constraints.md).


<a class="btn btn-primary" href="constraints.md">Learn more about REST</a>
