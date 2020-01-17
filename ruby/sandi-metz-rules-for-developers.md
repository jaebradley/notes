# [Sand Metz' Rules for Developers](https://thoughtbot.com/blog/sandi-metz-rules-for-developers)

* Classes can be no longer than 100 lines of code
  * Forces engineer to consider single responsibility principle
  * Smell can also be applied to specs - if specs are large then this probably means that the object under test is too large
* Methods can be no longer than five lines of code
  * Having only one line per branch forces using well-named private methods, which serve as good documentation as they should have clear names
* Pass no more than four parameters into a method - hash options are parameters
* Controllers can instantiate only one object
  * Views can only know about one instance variable and views should only send messages to that object
  * Use Facade pattern to handle when a page needs multiple things
    * Example is if page needs activity feed and notification counter
    * Create a `Dashboard` class that takes a user
    * And then wraps information about activities and notification counters by calling the appropriate API methods that serve as interface
