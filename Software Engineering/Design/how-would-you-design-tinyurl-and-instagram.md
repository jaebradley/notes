# [How Would You Design TinyURL And Instagram](https://medium.com/better-programming/how-would-you-design-tinyurl-and-instagram-987dfc06cbe9)

## TinyURL

* System is naturally read-heavy - estimated ratio of reads to writes is 100 to 1
* Traffic estimates
  * 500 million new URLs per month = 50 billion redirection
  * 200 new URLs per second or 2oK redirections per second
* Storage estimates
  * If every shortened link is stored for 5 years, the total number of objects (at 500 million new URLs every month) is 30 billion URLs
  * If each object is approvimately 500 bytes, will need 15 TB of total storage
* Can cache "hot" URLs that are frequently accessed
* One table to store information about the URL mapping and one table to store user-related information
* Because storing billions of rows, use a NoSQL database
* To encode the URL, compute a unique hash, which then is encoded in base 36 (a-z, 0-9) or base 62 (a-z, A-Z, 0-9) or base 64 if `+` and `/` are added
* If the key is 6 letters, there are ~68.7 billion possible strings - if the key is 8 letters there are 281 trillion possible strings
* MD5 algorithm returns a 128 bit hash value, which will return a string with 21 characters (each base 64 character encodes six bits of hash value)
  * Can take the first 6 / 8 letters of the key
  * To solve duplication, append a number from a sequence to each URL to make it unique even if multiple users provide the same URL

## Instagram

* Because photo uploads are a slower process because they go to disk, uploading users will consume all available connections
  * To address bottleneck, split reads and writes to separate servers
* To shard metadata, partition off photo id (if photo id was unique)
* Cannot have an auto-incrementing sequence in each shard to define photo id, because we use the photo id to find the shard where it will be stored
  * One solution is to have two tables, one that generates even-numbered ids and the other that generates odd-numbered ids
  * Load balance between the two databases to round-robin between the two databases
  * If the database servers are out of sync, that's fine, because the generated values should still be unique
