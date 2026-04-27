# [A Guide to Consistent Hashing](https://www.toptal.com/big-data/consistent-hashing)

* A hash function is a function that maps one piece of data to another piece of data (often an integer)
  * For example, some hash functions with an output range of `0..100` may map certain strings to the number `57` or `33`
  * Since thre are many more possible inputs than outputs, any output may have multiple inputs mapped to it (hash collision)
  * Good hash functions spread the input data as evenly as possible over all the different possible outputs
* Hash ring
  * Values from zero to max int are points on circle
  * Key is hashed to an int and then that's where it lies on ring
  * Hash server's IP address to place server on edge of circle
* Object keys and server keys are on hash ring
* Object belongs to whichever server whose key is closest in counterclockwise direction (or clockwise)
  * Keep sorted list of server values and then binary search list to find first server with value >= object key (or wrap around)
* To ensure object keys are evenly distributed amongst servers, assign many labels on key ring to each server
  * The relative weight of distribution between servers may depend on relative strength of servers (if server B is 2x the strength of all other servers it may have twice as many labels)
* If a server is removed, then labels are removed and keys should theoretically be redistributed amongst remaining labels / servers
* Similar concept occurs if a server is added - keys should theoretically be redistributed equally amongst additional labels / server
* In general, only `k` / `N` keys need to be remapped where `k` is number of object keys and `N` is number of servers

