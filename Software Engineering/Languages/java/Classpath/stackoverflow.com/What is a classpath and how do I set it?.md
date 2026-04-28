# [What is a classpath and how do I set it?](https://stackoverflow.com/questions/2396493/what-is-a-classpath-and-how-do-i-set-it)
* Classes are referenced by `import` ing from a specific package path
* JVM needs to know where to find these classes
* Impractical to have the JVM look through every folder on user’s machine, so user needs to provide the JVM a list of places to look
* User assigns folders and jar files on the classpath
* `MyClass` is in an `output` directory within a user’s project directory
* Let’s say `MyClass` is referenced by another class’s source file like `import org.some.package.MyClass`
* Then the `.class` file for `MyClass` would be at the path: `output/org/some/package/MyClass.class`
* In order to reference the`.class` file, the classpath would need to contain the `output` folder
* The entire package structure is not necessary since the `import` statement provides all the relative pathing to the JVM
* If `package` was bundled into a `.jar` file and then that `.jar` file was moved to a `lib` directory within the user’s project directory (like `lib/package.jar`), then the `.jar` file would need to be added to the classpath
* Then the JVM would look inside the `.lib/package.jar` file to find `org.some.package.MyClass`
* Classpaths can be set using environment variables like `export CLASSPATH=/home/someproject/lib/package.jar:/home/someproject/output/` or the `-cp` parameter when starting a Java program like `java -cp “/home/someproject/lib/package.jar:/home/someproject/output/“ MyMainProgramClass`
* Note that order of the entries in the class path matters - if the same class name exists in two paths, then the class on the first path will be used because it is found first