# Item 31: Use instance fields instead of ordinals

* May use ordinal of an enum to derive a value
  * Example is an enum of "ensembles" (solo, duet, trio, quartet) where there is a `numberOfMusicians` method that returns the ordinal + 1
  * However, hard to maintain, because if enums are reordered, the `numberOfMusicians` method will have completely different behavior
  * Also, if there are other ensembles that have the same number of musicians as other ensembles (double quartet, which has 8 musicians, the same as an octet) there's no way to do it
* Never derive a value associated with an enum from its ordinal - store it in an instance field instead (`SOLO(1)`, `DUET(2)`, etc)
