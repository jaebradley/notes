# Item 22: Prefer Custom Exceptions To Raising Strings

* When given a single string argument, `raise` will create a `RuntimeError` using the string as the error message
* WHen creating new exception classes, generally common practice to add `Error` suffix to exception class names
* Generally, should almost always inherit from another custom exception (that in turn, ends up inheriting from `StandardError`)
  * When omitting class name when handling exceptions with `rescue` it will intercept any exception whose class inheritance hierarchy includes `StandardError`
  * Remember to initialize the class with a call to `super` so the upstream `Exception` class can set up instance variables like the error message
* When calling `raise` with a class or an object, it will send the `exception` message to that object
  * The `Exception` class and instances variables both have an `exception` method
  * On the `Exception` class, the `exception` class method delegates to `new` and raises the resultant `Exception` instance object
  * On an `Exception` instance, if no arguments are given, the `exception` method will simply return `self` - so in the case of `raise(MyCustomException.new)`, the `MyCustomException` instance will be the raised exception
    * If an `Exception` instance and an error message are given, it will invoke `exception` method with the error message argument
    * This will copy the exception, reset error message to passed in error message, and return the copy - then this coppy is raised
    * This can get confusing when custom errors create custom error messages based on how they're initialized
    * So when dealing with custom errors, prefer using single-argument version of `raise`