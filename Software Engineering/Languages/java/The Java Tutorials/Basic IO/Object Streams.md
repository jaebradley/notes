# [Object Streams](https://docs.oracle.com/javase/tutorial/essential/io/objectstreams.html)

* Just as data streams support I/O of primitive data types, object streams support I/O of objects
  * Most, but not all, standard classes support serialization of their objects
  * Those that support serialization of their objects implement the marker interface `Serializable`
* `ObjectInputStream` and `ObjectOutputStream` implement `ObjectInput` and `ObjectOutput` which are subinterfaces of `DataInput` and `DataOutput`
  * So an object stream can contain a mixture of primitive and object values
* If `readObject` is able to reconstitute an object from a stream, it has to be able to reconsistute all of the objects the original object referred to
  * These additional objects have their own references, etc
  * `writeObject` traverses the entire web of object references and writes all objects in that web onto the stream
* If two objects on the same stream both contain references to a single object, they will both refere to a single object when they're read back
* Two calls to `writeObject` that reference the same object will result in two variables when calling `readObject` twice that refer to the same object
* If an object is written to two different streams, a single program reading both streams back will see two distinct objects
