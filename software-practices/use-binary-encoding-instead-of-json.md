# [Use Binary Encoding Instead Of JSON](https://medium.com/better-programming/use-binary-encoding-instead-of-json-dec745ec09b6)

* Textual format like XML and CSV cannot distinguish between strings and numbers
* CSV doesn't contain any type of schema leaving it to the application to define the meaning of each row and column
* Textual formats take up more space than binary encoding because they need to contain field names as well (like JSON and XML)
* Binary encoding like Thrift or Protocol Buffer require a schema for data to be encoded

```protobuf
message Person {
  required string user_name = 1;
  optional int64 favourite_number = 2;
  repeated string interests = 3;
}
``

* There is a data type (string) and a "tag number" (`1`, `2`, `3`) for each field
* So a `user_name` value like `Martin` can be stored in `13` bytes
* `0b` is `type 11` which is `string` (`1` byte)
* `00 01` is the field tag (i.e. `1`)` (`2` bytes)
* `00 00 00 06` is the stored length of the value (for `Martin`, this is `6`) (`4` bytes)
* `4d 61 72 74 69 6e` is `M a r t i n` (`6` bytes)
* Leads to much more compact data representation, and consumes much less memory overall
