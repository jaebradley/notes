# [Storing Hundreds of Millions of Key Value Pairs in Redis](https://instagram-engineering.com/storing-hundreds-of-millions-of-simple-key-value-pairs-in-redis-1091ae80f74c)

* Instagram had to keep mapping of around 300 million photos back to their user (`media_id`s to `user_id`s)
* Their requirements were
  * Quick retrieval system
  * Fit data into memory (`EC2` @ `17GB` or `34GB`)
  * Persistent (no need to rebuild after server failure)
* SQL database seemed overkill since IDs never get updated (only inserted) and data didn't need to be transactional or relational
* Using Redis to store keys and values via something `SET media:1234 user_5678` and `GET user_5678` (which would retrieve `media:1234`)
  * Redis would need `70MB` for 1 million keys or at least `21GB` for 300 million
  * Redis can intermittently background save - Instagram's Redis deployments run as master/slaves where the slave saves to disk every minute
* Used Redis hashes to bucket `media_id`s, and then within each bucket the `media_id` would serve as the key within _that_ hash to get the user id
  * The idea here is that Redis hashes can encode their values very efficiently - more efficiently than simple Redis' key / value storing?
  * Also, hash lookups are theoretically `O(1)`-ish, so still quite fast
* Used `1000` to divide media ids arbitrarily, so `12345` => look in bucket `12` => for key `12345` and return value
* They used `1000` as the maximum number of entries a hash can have while still being efficient to access
  * any more than `1000` and they said that the `HSET` command returned noticeable CPU activity
* Using this hashed lookup system, the size of 1 million keys was only 16MB, so 300 million keys would be under 5GB
