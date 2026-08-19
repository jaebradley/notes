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

## Compressed Hashing Scheme for Dictionary Lookups
* Stored the _hashes_ of the words in a hash table
* While individual words can vary in length, the hash function should compress these words into a fixed number of bits
* In order to handle hash collisions, the hash code needs to be long enough to support the minimum acceptable collision tolerance
* A hash code of size `b` bits has a total hash code space of `2 ^ b` hash codes
* If the size of the dictionary is `V`, then the probability of a hash collision is `V / (2 ^ b)`
* Given that a collision rate of `1/(2 ^ 12)` was acceptable, a hash code size of `27` bits is needed

## The Theoretical Minimum Limit of Hash Code Compression
* The probability of an event and the bits needed to encode it are related
* A 100% probable event needs no information to be stored (i.e. 0 bits are necessary to encode it)
* Given some math, and given a dictionary size of `30,000` words the minimum number of bits needed to encode a single hash code is `~14` bits, which is `~50%` less than the original `27` bit hash code

## Delta-Based Compression Scheme
* Instead of compressing raw hash codes, they computed and stored differences between successive hash codes, stored in sorted order
* By working with hash differences, the differences were smaller than the raw hash codes, and thus, more memory efficient
  * Difference values would repeat leading to more effective compression
* Finding a hash code meant starting with the first hash code, and then adding each of the differences until the matching hash code was identified
* One of the basic principles behind lossless compression is to assign shorter codes to symbols with higher probabilities and longer codes to symbols with lower probabilities
  * Requires computing the probability distribution of all the symbols in the data set
* Holding a probability distribution table for 30k symbols in memory would eliminate any compression advantage from this hash-based approach
* Computing the hash difference probabilities would not have been possible to do in-memory, so an expensive disk-based solution would have been necessary to compute these probabilities

## Encoding Algorithm
* Hash difference values follow a pattern of exponential decay
* Segment the hash difference values into blocks of size `m`
* Each value within a block is assigned a code of `k` bits
* The next block gets codes of size `k + 1` bits
* The minimum number of bits required to encode the outcome of an event is calculated by its information content - `-log (probability of an event)`
* If the event has a `1/2` probability, it needs a `1` bit code, an event with probability `1/4` needs `2` bits, etc

### Example
* The block size is `5` and the codes in the first block are `4` bits wide (starting `k` is `4`)
* First code in the first block is `0110`
* Next four block codes are `0111`, `1000`, `1001`, `1010`
* The next block (block `2`) would naturally start at `1011` (`1` + the last code in block 1 (`1010`))
* However, since this code is in the next block, its bit width needs to be `1` bit larger
* So left bit shift `1011` to `10110`
* Note that the last `4` bits of `10110` are the same as the first code in the first block (`0110`)

