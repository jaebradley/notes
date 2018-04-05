# Part 3 - Cache

* Whenever your application has to read data, it should at first try to retrieve the data from your cache
  * Because cache is very fast because it holds dataset in RAM - Redis can do several hundreds of thousands of reads per second

## Cached Database Queries

* Common caching pattern that hashes database query and stores in cache along with result dataset.
* Next time the same query is run, instead of returning directly from db, the cached result dataset is returned.
* Main issue is expiration - how to know when to delete a cached result if a particular table cell it depends on has changed?

## Cached Objects

* Serialize classes and their instances and store their data in a cache
* Imagine a class called "Product" which has a property called data
  * This data is an array containing prices, text, pictures, reviews, etc.
  * Data is built by the class doing a non-trivial amount of work (joining information from different sources, for example)
  * So store this data property / instance of Product in cache
  * Then can use instance ID to evict from cache when something does change
    * This too gets tricky if data property can be updated in multiple places
