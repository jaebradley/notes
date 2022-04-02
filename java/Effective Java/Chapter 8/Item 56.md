# Item 56: Adhere to generally avveoted naming conventions

* There is little consensus as to whether acronyms should be uppercase or have only their first leter capitalized
  * A strong argument can be made in favor of capitalizing only the first letter: in the case of multiple acronyms, can you tell where the start of one acronym starts and ends? (i.e. `HTTPURL` vs. `HttpUrl`)
* Classes are generally named with a singular noun or noun phrase like `Timer`
* Interfaces are named like classes, or with an adjective ending in `able` or `ible` like `Comparable`
* Methods are generally named with a verb or verb phrase (`append` or `drawImage`)
  * Methods that return a `boolean` usually have names that begin with `is` or `has`
  * Methods that return a non-`boolean` funcion or attribute of the object are usually named with a noun or a verb phrase beginning with the verb `get`
  * Methods that convert the type of an object, returning an independent object of a different type are often called `toType`
  * Methods that return a _view_ (`Item 5`) are often called `asType`
  * Methods that return a primitive with the same value as the object on which they're invoked are often called `typeValue` like `intValue`
  * Factory methods are usually called `valueOf`, `of`, `getInstance`, `newInstance`, `getType`, `newType`
