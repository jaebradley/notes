# [How Well Do You Actually Understand Annotations In Java?](https://blog.usejournal.com/how-much-do-you-actually-know-about-annotations-in-java-b999e100b929)

```java
@interface SampleAnnotation {
  String occupation();
  int age();
}

@SampleAnnotation (occuptation="Cook", age=21)
```

* Note the `@interface` which tells the compiler that this is an annotation type
  * Annotations are based off of the same plumbing behind interfaces
  * The method declarations are implemented by the compiler
  * The methods are effectively treated by fields when using them
* Annotations are merely metadata, they do not change the nature of a program, at least not directly
  * They can tell development tools to do a certain action based on the type of annotation
* Annotations cannot participate in inheritance
* Annotation methods do not have arguments
* Annotations cannot be generic or specify a throws clause
* Annotations must return an enum, primitive type, another Annotation, a String, or a Class object (or an array of these types)
* Single-member annotations are annotations with only one member with a member named `value`
  * These members can also have default values


## Retention Policies

* They specify how long an Annotation is to be retained
* `CLASS` means that annotations are recorded in the class file by the compiler but need not be retained by the VM at runtime
* `RUNTIME` means that annotations are recorded in the class file by the compiler and retinaed by the VM at runtime, so they may be read reflectively
* `SOURCE` - annotations are discarded by the compiler

## Annotation Introspection

* For `RUNTIME` annotations, can call the `getClass` method on an object, and then the `getContructor().getAnnotations()` / `getField('some field name').getAnnotations()` / etc. to get the annotations on the constructor / methods
* Can also call the `Class.getAnnotations()` method to get the annotations on the `Class` definition itself

## General Formula For Creating Annotations

* If the annotation should be repeatable, create an annotation, mark it as repeatable by using the `@Repeatable` annotation
  * Create a container for the annotation's instances with a single array of annotation instances that is referenced by a single `value()` method
* Type annotations have a `@Target` of `ElementType.TYPE_USE` and are useful for compiler plugins, build tools, etc.
* Marker annotations are annotations without any members (like `@Override`)
