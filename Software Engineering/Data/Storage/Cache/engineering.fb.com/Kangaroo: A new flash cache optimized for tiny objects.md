# [Kangaroo: A new flash cache optimized for tiny objects](https://engineering.fb.com/2021/10/26/core-infra/kangaroo/)
* Cache for tiny objects (~100 bytes)
* Flash caches are set-associative and log-structured
* Set-associative caches write more bytes than necessary because they write a 4 KB flash page for every object
* ~40x more bytes than necessary when writing objects that are ~100 bytes in size
* Log-structured caches have large DRAM overhead because they have to keep an index entry for every on-flash object
* KLog is a small log-structured flash cache
* KSet is a large set-associative flash cache
* Each KSet has a Bloom filter to check if an object could be in that KSet
* Amortizes writing the 4 KB flash page by writing multiple objects
* Uses ~5% of KLog’s flash capacity to hold these objects
* Evicts objects instead of admitting them to KSet if there are fewer than n objects to insert from KLog
* For large caches, it is more efficient to use flash than to use DRAM
* Edges in Facebook’s social graph average under 100 bytes
