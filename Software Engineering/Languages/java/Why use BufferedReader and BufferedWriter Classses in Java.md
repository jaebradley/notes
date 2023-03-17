# [Why use BufferedReader and BufferedWriter Classses in Java](https://medium.com/@isaacjumba/why-use-bufferedreader-and-bufferedwriter-classses-in-java-39074ee1a966)

* For unbuffered I/O streams, each read or write request is handled directly by the underlying OS
  * This can make a program much less efficient since each such request often triggers disk access, network activity, or some other operation that is relatively expensive
* Each read request made by a `Reader` causes a corresponding read request to be made by the underlying character or byte stream
  * It is therefore good practice to wrap a `BufferedReader` around any `Reader` whose `read()` operation may be costly, such as `FileReader`s and `InputStreamReader`s
* Without buffereing, each invocation of `read()` could cause bytes to be read from the file, converted into characters and then returned, which can be very inefficient
* A data buffer is a region in memory that is written to, and then when a flush operation is performed, will then write the buffer to a file (for example)
* `BufferedWriter`s have a significantly larger memory buffer than `Scanner` and `PrintWriter`
* `Scanner`s are memory and CPU inefficient relative to `BufferedReader` because `Scanner` uses regular expressions
