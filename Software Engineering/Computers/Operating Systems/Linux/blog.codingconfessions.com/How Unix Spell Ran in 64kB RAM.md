# [How Unix Spell Ran in 64kB RAM](https://blog.codingconfessions.com/p/how-unix-spell-ran-in-64kb-ram)
* The spell checker for unix needed to fit a 250 KB dictionary in 64 KB of RAM while still performing fast lookups
  * Even with modern compression techniques, a 250 KB file could not be compressed to less than 85 KB

## Affix Removal Algorithm
* Iteratively remove common prefixes and suffixes from a word
* Look up these progressive word fragments to see if they are present in the dictionary
* `misrepresented` -> `present` (which is in dictionary) by removing prefixes `mis`, `re` and suffix `ed`
* This algorithm was not 100% accurate (misspelled words were not always identified)
* Because of the usage of this algorithm, the final dictionary could contain much less information than the original 250 KB dictionary
  * Final dictionary consisted of 25,000 words

## A Bloom Filter-based Lookup
* Still not possible to load the 25,000 word dictionary into 64 KB of RAM
* Bloom filter is a bit table (initialized to all `0`s)
* To add an item to this bit table, apply `k` hash functions to the item
  * Hash functions should return a value representing an index in the bit table
  * Flip the bit at each returned index value in the bit table to `1`
* To look up an item to see if it might exist in the Bloom filter, reverse the process
  * Apply the `k` hash functions to the lookup item and see if the corresponding value index is set to `1` in the bit table
  * If at least one of returned bit table values is `0`, then the item is definitely not present
  * If all the returned bit table values is `1`, then the item _might_ be present due to hash collisions
* A false positive rate of 1 in 2000 was acceptable
  * Based on a fixed dictionary of 25,000 items, and a bit table size of 400k bits (due to limited RAM), they used 11 hash functions to achieve this false positive rate
* However, they kept finding words to add to the dictionary, so the dictionary size kept increasing (went from 25,000 to 30,000)
  * A bigger dictionary required a larger bit table, which was not possible from a memory perspective
