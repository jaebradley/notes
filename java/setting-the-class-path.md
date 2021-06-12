# [Setting the class path](https://docs.oracle.com/javase/7/docs/technotes/tools/solaris/classpath.html)

* The class path is the path that the Java runtime environment searches for classes and other resource files
  * Can be set using the `-classpath` option when calling a JDK tool (preferred) or by setting the `CLASSPATH` environment variable
  * `-classpath` option preferred because can set it individually for each application without affecting other applications and without other applications modifying its value
* The class path tells the JDK tools and applications where to find third-party and user-defined classes
  * The class path needs to find any classes you've compiled with the javac compiler
* The JDK, JVM find classes by searching the Java platform classes, any extension classes, and the class path in that order
* Only need to set the class path when you want to load a class that's not in the current directory or any subdirectories and not in a location specified by the extensions mechanism
* Suppose you want the Java runtime to find a class named `Cool.class` in the package `utility.myapp`
  * The path to the directory is `/java/MyClasses/utility/myapp` so you would set the class path so that it contains `java/MyClasses`
  * Files which are part of the same package may actually exist in different directories - the package name will be the same for each class, but the path to each file may start from a different directory in the class path
* To use a class library that is in a `.jar` file, the command would look something like this: `java -classpath /java/MyClasses/myclasses.jar utility.myapp.Cool`
* To find class files in the directory `/java/MyClasses` as well as classes in `/java/OtherClasses`, you would set the class path to: `java -classpath /java/MyClasses:/java/OtherClasses`
* The Java interpreter will look for classes in the directories in the order they appear in the class path variable
  * Java interpreter will first look in the directory `/java/MyClasses`. If a class is not found with the proper name, the interpreter will look in the `/java/OtherClasses`
