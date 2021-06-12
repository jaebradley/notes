# [How Classes are Found](https://docs.oracle.com/javase/7/docs/technotes/tools/findingclasses.html)

* The Java launcher, `java`, initiates the Java virtual machine
  * The virtual machine searches for and loads classes in this order
    * Bootstrap classes: Classes that comprise the Java platform, including the classes in `rt.jar` and other important `jar` files
    * Extension classes: Classes that use the Java Extension mechanism - bundled as `.jar` files in the extensions directory
    * User classes: Classes defined by developers and third parties that do not take advantage of the extension mexchanism
* Bootstrap classes are in the `rt.jar` and other `jar` files in the `jre/lib` directory
* To find user classes, the launcher refers to the user class path - a list of directories, JAR archives and ZIP archives which contain class files
* A class files has a subpath name that reflects the class's fully-qualified name
  * For example, if the class `com.mypackage.MyClass` is stored under `/myclasses`, then `/myclasses` must be in the user class path and the full path to the class files must be `/myclasses/com/mypackage/MyClass.class`
  * If the class is stored in an archive named `myclasses.jar`, then `/myclasses.jar` must be in the user class path, and the class file must be stored in the archive as `com/mypackage/MyClass.class`
* A JAR file usually contains a "manifest" - a file which list the contents of the JAR
  * The manifest can define a JAR class-path, which further extends the class path (but only when loading classes from the JAR)
