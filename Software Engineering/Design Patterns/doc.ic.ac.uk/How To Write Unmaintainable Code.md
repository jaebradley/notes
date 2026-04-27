# [How To Write Unmaintainable Code](https://www.doc.ic.ac.uk/%7Esusan/475/unmain.html)
* Make sure every method does a little bit more (or less) than its name suggests
  * As an example, a method named `isValid` should convert to binary and store the result in a database
* Make sure there are at least 25 places in the code that need to be modified if you were to add another airline
* In Java, disdain the interface
  * Put lots of functionality in your classes that can only be used by classes that inherit from them
  * Use lots of `instaneof` checks in your methods
* Make all of your leaf classes `final`
  * Certainly no one could improve on your work by extending these classes
