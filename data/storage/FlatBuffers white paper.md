# [FlatBuffers white paper](https://google.github.io/flatbuffers/flatbuffers_white_paper.html)

* Currently, bottle neck is no longer CPU speed, but memory, meaning that making an efficient application should start and finish with thinking about memory
  * How much you use, how you lay it out, how you access it, how you allocate it, when you copy it
* Serialization is a common source of memory inefficiency, with lots of temporary data structures needed to parse and represent data

## Summary

* A FlatBuffer is a binary buffer containing nested objects (structs, tables, vectors) organized using offsets so that the data can be traversed in-place just like any pointer-based data structure
* Unlike most in-memory data structures, it uses strict rules of alignment and endianness (always little endian) to ensure that buffers are cross platform
* Object types are defined in a schema, which can then be compiled to C++ or Java

## Tables

* Tables use extra indirection to access fields through a `vtable`
* Each table comes with a `vtable`, which may be shared between multiple tables with the same layout and contains information where fields for this particular kind of instance of `vtable` are stored
* The `vtable` may also indicate that the field is not present, in which case a default value is returned

## Schemas

* The strong typing of data definitions means less error checking/handling at runtime
* A schema enables us to access a buffer without parsing
* FlatBuffers deprecate fields instead of manual field id assignment like in Protocol Buffers
  * Extending an object in a .proto means hunting for a free slot among the numbers
  * Removing fields are a problem - either have to keep them, which doesn't make it obvious that this field should not be read/written anymore (and still generate accessors). Or remove the fields but risk that there's old data that uses the field, with nasty consequences
