# [Model Once, Represent Everywhere: UDA (Unified Data Architecture) at Netflix](https://netflixtechblog.com/uda-unified-data-architecture-6a6aee261d8d)
* Transpile domain models into schema definition languages like GraphQL, Avro
* Disover and explore domain concepts via search and graph traversal
* Programmatically introspect the knowledge graph
* Primary data management service which is the authority on domain models
* PDM maps domain models to Avro and GraphQL schemas
* “Sphere” catalogs and relates business concepts like “actors” and “movies”
* Sphere walks the knowledge graph, generating SQL queries to retrieve data without any sort of manual technical intervention required
* UDA uses a named graph-first information model using a language called Upper
* Upper captures business and system domains and concepts into organized domain models
* Domain models are controlled vocabularies that define classes of keyed entitites, their attributes, their relationship to other entities, which may be keyed or nested, within the same domain or across domains
* Upper is self-referencing - it models itself as a domain model, and self-validating, because it conforms to its own model

## Data Container Representations
* Instance data that conform to their own schema language or type system
* Avro records, table rows, Java API objects
* These specific representations *are* data and modeled as system specific domain models

## Mappings
* Every element in a domain model is addressable, from the domain model itself to specific attributes and relationships in that domain model
* Data containers are also addressable from a table to an individual column or from a GraphQL type to a specific field
* Mappings connect nodes in a subgraph of the domain model to nodes in a subgraph of a data container representation

## Projections
* Projections produce concrete data containers, like a GraphQL schema
* These data containers implement the characteristics of the registered domain model
* Transpile a domain model into the target container’s schema language (GraphQL and Avro at the moment)

Example: https://github.com/Netflix-Skunkworks/uda/tree/9627a97fcd972a41ec910be3f928ea7692d38714/uda-intro-blog
