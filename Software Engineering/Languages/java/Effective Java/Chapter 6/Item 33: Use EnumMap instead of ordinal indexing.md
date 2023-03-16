# Item 33: Use `EnumMap` instead of ordinal indexing

* Example is grouping an `Herb` enum that has a `Type` by all the `Herb`s of the same `Type` and using the `Herb`'s ordinal as it's index in the array
* This is done by constructing three sets, one for each type, and iterating through the herbs, placing each herb in the appropriate set by putting the sets into an array indexed by the type's ordinal
* The problem with this technique is that when you access an array that is indexed by an enum's ordinal, it is your responsibility to use the correct `int` value
  * If you provide the wrong value, the program will silently do the wrong thing
* Instead, use an `EnumMap` of `Type` as the key and `Set<Herb>` as the value
  * Insternally, the `EnumMap` uses an ordinal-indexed array
