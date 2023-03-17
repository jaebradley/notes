# [Schema Evolution In Avro, Protocol Buffers and Thrift](http://martin.kleppmann.com/2012/12/05/schema-evolution-in-avro-protocol-buffers-thrift.html)

* Assume a person object - so something like

```json
{
  "userName": "Martin",
  "favouriteNumber": 1337,
  "interests": ["daydreaming", "hacking"]
}
```

* Without whitespace, it's 82 bytes

## Protobuf

```protobuf
message Person {
  required string user_name = 1;
  optional int64 favourite_number = 2;
  repeated string interests = 3;
}
```

* Encoded, uses 33 bytes
* Each field starts with a byte that indicates its tag number (1, 2, 3) and the type of field
* If the first byte of a field indicates that the field is a string, it is followed by the number of bytes in the string and the UTF-8 encoding of the string
* If the first byte indicates that the field is an integer, a variable-length encoding of the number follows
* No array type, but a tag number can appear multiple times to represent a multi-valued field
* There is no difference in the encoding between optional / required / repeated (just the number of times the tag number can appear)
  * This imiplies that you can change a field from optional to repeated or vice versa
  * If the parser is expecting an optional field but sees the same tag number multiple times in one record it discards all but the last value
* required has an additional validation check so if you change it, risk runtime errors (sender of message thinks it's optional but the recipient thinks it's required)
* Must never reuse the tag number for another field in the future because you may still have data stored that uses the tag for the field that was deleted
* Add a field to a record but use a new tag number
* Can rename fields because field names don't exist in binary serialization but you can never change a tag number

## Avro

* Can be written via a JSON format or using an IDL

```json
{
    "type": "record",
    "name": "Person",
    "fields": [
        {"name": "userName",        "type": "string"},
        {"name": "favouriteNumber", "type": ["null", "long"]},
        {"name": "interests",       "type": {"type": "array", "items": "string"}}
    ]
}
```

```avro
record Person {
    string               userName;
    union { null, long } favouriteNumber;
    array<string>        interests;
}
```

* Strings are just a length prefix followed by UTF-8 bytes, but there's nothing int he bytestream that tells you that it is a string
  * Could be a variable-length integer, for example
  * Only way to parse the binary data is by reading it alongisde the schema, and the schema tells you what type to expect next
  * Need to have the _exact same version_ of the schema as the writer of the data
* Avro encoding does not have an indicator saying which field is next - just encodes one field after another in the order they appear int he schema
* No such thing as an optional field in Avro - if you want to be able to leave out a value, use the `union` type like `union { null, long }`
* If a new type is added to a union, first need to update all readers / consumers with the new schema and then update the writers so that they can use the type in the records they generate
* Can reorder fields however you like - although fields are encoded in the order they are declared, the parser matches fields in the reader and writer schema by name, which is why no tag numbers are needed in Avro
* This implies that changing the name of a field is tricky - need to first update all readers of the data to use the new field name, keeping the old name as an alias, and then update the writer's schema to use the new field name
* Adding a field to a record implies that there is some default value because a reader using the new schema must be able to parse a record written in the old schema (which lacks the field) so it can fill in the default
* Can remove a field from a record, provided it previously had a default value (good reason to give _all_ fields a default value, if possible)
  * This is so taht when a reader using the old schema parses a record written with the new schema it can fall back to the default

