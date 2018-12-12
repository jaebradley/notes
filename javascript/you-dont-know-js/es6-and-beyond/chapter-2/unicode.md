# [Unicode](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/ch2.md#unicode)

* Unicode characters from `0x0000` to `0xFFFF` are called the Basic Multilingual Plane (BMP)
* There are more Unicode characters which range up to `0x10FFFF` - these are called the astral symbols
* Before `ES6`, JavaScript strings could specify Unicode characters like this `\u2603`
  * However, this supports four hexademical characters (i.e does not support the astral symbols)
  * In order to represent the astral symbols using Unicode characters need to use surrogate pair (two Unicode-escaped characters side-by-side)
    * `\uD834\uDD1E` - G-Clef symbol, for example
* `ES6` introduced Unicode point escaping - now G-Clef symbol is `\u{1D11E}`
* G-Clef symbol has a length of `2`
* Correct way to find length of string - `[...gclefSymbol].length` or `Array.from(gclefSymbol).length`
* `charAt` does not respect astral symbols
