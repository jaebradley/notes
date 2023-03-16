# [Replacing If-Else With Commands And Handlers](https://levelup.gitconnected.com/replacing-if-else-with-commands-and-handlers-527e0abe2147)

* Situation - an `if/else` block that basically says "when user update reason is X, execute this logic, else when user update reason is Y, execute this other logic, etc"
  * Need to add a new branch for every update reason - violation of open/closed principle (basically, adding new functionality should not involve changing existing code)
* The idea behind command + handlers + dispatch is to dispatch one command that may invoke multiple handlers
  * New functionality can be added without touching existing code
  * Controllers should not contain any business or persistence logic
  * A command can be dispatched without the caller knowing the concrete handlers
  * Every handler matching the command needs to be executed
  * New commands or processing steps do not require you to modify existing code
* Controller is only focused on validating the correctness of the data it receives - not how data is handled after dispatching the command
* Commands should only focus on immutability and data correctness
  * No setters, and ensure that input data is valid
  * Plain old regular classes
* Handlers share interface with single method - takes a `Command` interface and returns a `Task` object
* Dispatchers take a command and grab all matching handlers and pass the command to each handler
  * So keep track of all command values and the matching handlers in a `Map`
