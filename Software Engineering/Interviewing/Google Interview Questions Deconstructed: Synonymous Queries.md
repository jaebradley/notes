# [Google Interview Questions Deconstructed: Synonymous Queries](https://alexgolec.medium.com/google-interview-problems-synonymous-queries-36425145387c)

* Suppose there are two inputs - a list of pairs of strings representing synonyms where the two strings in the pair are synonymous with one another, and a list of pairs of strings representing queries

```python
SYNONYMS = [
  ('rate', 'ratings'),
  ('approval', 'popularity'),
]

QUERIES = [
  ('obama approval rate', 'obama popularity ratings'),
  ('obama approval rates', 'obama popularity ratings'),
  ('obama approval rate', 'popularity ratings obama')
]
```
* Clarifying questions like "can words have multiple synonyms", "does word order matter", "are synonym relationships transitive"
  * There are also trivial questions, like "does capitalization matter" that don't affect the core algorithmic problem
* High-level overview of the solution is to break the query up into words and compare pairs of words to see if they are identical or synonymous with one another
* Doing something like `elif (w1, w2) in synonyms` where `synonyms` is a list is a linear search in Python
  * The `in` keyword is syntactic sugar for a contains method - unless list is small, this is a performance killer
* Need a constant-time synonym lookup - there's always a hashmap or hashset behind constant-time lookups
* Assigning / associating synonyms often time leads to an assignment of both words to each other - but do they really need to be assigned to each other?
  * Can just assign one as the key and the other as the value and lookup whether a pair exists by checking either value in the pair is a key in the mapping of synonyms
* An additional wrinkle is synonym transitivity meaning that if words `A` and `B` are synonymous and if `B` and `C` are synonymous then words `A` and `C` synonymous
  * Naive approach is whenever adding a word add it to all the corresponding sets for all the words currently in that set
    * This grows quadratically with size of the synonym set
  * Can use a Disjoint Set which allows you to determine if two items belong to the same set
  * These sets are represented by trees where each item has a parent
    * Every tree has a root, which means it's possible to determine if two items belong to the same set by following their parents until we find a root element for each
    * If the two elements have the same root, they must belong to the same set
    * Joining sets involves finding the root elements and making one the root of the other
  * Compaction is built into the disjoint set as it's building the root-finding operations
    * Compaction eliminates duplicate work when trying to resolve transitive properties
    * If there is some synonym transitivity that looks like `speedy -> rapid -> quick -> snappy -> fast` then the resolution would be that `speedy -> fast` and `rapid -> fast` etc and thus duplicate work is eliminated when identifying the root for a given synonym
