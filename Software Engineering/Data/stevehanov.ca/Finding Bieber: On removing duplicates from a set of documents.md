# [Finding Bieber: On removing duplicates from a set of documents](https://stevehanov.ca/blog/finding-bieber-on-removing-duplicates-from-a-set-of-documents)
* Author's example problem is a file containing many song lyrics
  * Author wants to know whether two songs are similar, and to do this comparison quickly
* Take lyrics, and turn them into a bag of words - a set of words, and their counts (without order)

## Jaccard distance function
* This distance function is inspired by the botanist Paul Jaccard who compared the plants in different regions: # of species common to two regions / # of total species across both regions

```python
def jaccard(a, b):  
    intersection_size = len(a & b)
    union_size = len(a | b)
    return 1.0 - float(intersection_size)/union_size
```
* For comparing songs, if two songs differ by one word, then the distance is small, and if they differ by many words, the distance should be large
* For the two million songs that exist in the file, this distance function needs to run for every pair of songs: 4 trillion comparisons in all
  * This is not really practically feasible

## MinHash
* Search engines also have to deal with duplicates where two duplicate documents might be returned as search results
* MinHash is a hash function for documents where documents that have similar bags of words have close MinHash values
* Build dictionary of all possible words, in some order, like lexicographical
* MinHash introspects a document and assigns it a score based on the minimum dictionary word index of any word in the document
* Can use a set of dictionaries that have different orderings and combine their collective MinHash scores
  * Eventually, using a large-enough set of dictionaries means that documents that share similar sets of words start hashing to similar values

