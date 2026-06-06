# [Write code that is easy to delete, not easy to extend.](https://programmingisterrible.com/post/139222674273/write-code-that-is-easy-to-delete-not-easy-to)
* > “Start with a list of difficult design decisions which are likely to change. Each module is then designed to hide such a decision from the others.” 
* Instead of breaking code into parts of common functionality, break code apart by what it does not share with the rest
  * Isolate the most frustrating parts to write, maintain, or delete away from each other.
* > “Each hard problem is only handled by one module”
* Loose coupling is about being able to change your mind without changing too much code
