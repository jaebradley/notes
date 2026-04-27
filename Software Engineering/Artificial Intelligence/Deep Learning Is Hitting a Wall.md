# [Deep Learning Is Hitting a Wall](https://nautil.us/deep-learning-is-hitting-a-wall-238440/?utm_source=pocket_collection_story)

* Deep learning, which is fundamentally a technique for recognizing patterns, is at its best when all we need are rough-ready results
  * Low stakes and do not need perfect results
  * Rabbit photo tagging example - photo was not explicitly tagged, but rabbit photo was similar enough to other photos in some database of other rabbit-labeled photos
* Deep learning systems are particularly problematic when it comes to outliers that differ substantially from things on which they are trained
  * Tesla FSD failed to recognize partially obscured person holding up a stop sign in the middle of a road
* Set of scaling laws for neural network models of language - the more data they fed into their neural networks, the better those networks performed
  * AI would get better and better if we gathered more and more data and applied deep learning at increasingly large scales
  * larger GPT-3-like models have increased fluency but are not more trustworthy
* The deep learning hope is that intelligent behavior will emerge purely from the confluence of a massive set of data
* NetHack Challenge - a game that requires a player to explore a dungeon
  * Various AIs participated, however a pure symbol-manipulation based system beat the best deep learning entries
  * Dungeon is generated anew every game, which means that memorization / approximation of a game board is not sufficient to win
  * Deep-learning systems are outstanding at interpolating between specific examples they have seen before, but frequently stumble when confronted with novelty
* Simple algebra has variables, operations, and bindings (let x = 12 for the purpose of some calculation)
* Spell-checking example
  * When you have Google-sized data, look at the logs of how users correct themselves
  * If they look for "the book" after looking for "teh book" you have evidence for what a better spelling for "teh" might be
