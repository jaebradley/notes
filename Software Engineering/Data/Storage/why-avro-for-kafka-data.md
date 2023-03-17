# [Why Avro For Kafka Data?](https://www.confluent.io/blog/avro-kafka-data/)

* Avro is an open-source data serialization system that helps with data exchange between systems, programming languages, and processing frameworks
  * It defines a binary format for your data (and can map it to the programming language of your choice)
* Avro has the following advantages
  * Very compact (JSON repeats every field name for every single record, so inefficient for high-volume data)
  * Can generate Java objects (for example) / has other bindings with other programming languages
  * Schema language is defined in pure JSON
* Benefits of these data object schemas is that they
  * Communicate the set of fields for consumers and producers
  * Can be self-documenting via the `doc` field
  * Specify the data _type_ so that malformed data is not permitted
* Use enumerated values wherever possible instead of magic strings
* Require documentation for all fields
* Avoid non-trivial union types and recursive types as they are Avro features that map poorly to mother other systems
