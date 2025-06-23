# [Named graph](https://en.wikipedia.org/wiki/Named_graph)
* Set of Resource Description Framework statements (a graph) are identified using a URI
* One mental model of the public internet is a graph of document nodes identified with URIs and connected by hyperlink arcs expressed within HTML documents
* “follow your nose” approach applies to RDF documents where URIs within the RDF point to other resources
* Named graphs are the formalization that the contents of an RDF document (a graph) are named by the URI of the document
* Trust is managed by the publisher applying a digital signature to the data in the named graph
* `http://example.org/joe` is an example named RDF document
* The “graph” data is
  * `http://example.org/joe` is a Person
  * `http://example.org/joe` homepage is at `http://example.org/joe/index.html`
  * `http://exaple.org/joe` mailbox is at `mailto:joe@example.org`
  * `http://example.org/joe` name is `“Joe Test”`
