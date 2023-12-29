# [System Design Interview: Scalable Unique ID Generator (Twitter Snowflake or a similar service)](https://medium.com/double-pointer/system-design-interview-scalable-unique-id-generator-twitter-snowflake-or-a-similar-service-18af22d74343)

* Target requirements
  * Fixed size ID (as small as possible, usually 64 bit requirement)
  * ID should be unique (i.e. no duplicates)
  * ID should be sortable (usually accomplished by including a timestamp as part of the ID)
* UUIDs are 128-bit numbers that have low chances of duplication
  * Too large (i.e. 2x size of target requirement)
  * UUIDs are generally non-sequential (i.e. random, which is good for not being able to be predicted in advance, but does not fulfill sortable requirement)

## Twitter Snowflake

* Idea is to fill the 64 bit ID space with a timestamp (to fulfill sortability requirement) and the remaining bits fulfill the unique identification requirement
* Leftmost 41 bits are for a timestamp
  * Supports millisecond precision for 69 years
  * `(2^41 - 1 / (milliseconds in a year))`
* Next 10 bits are a machine ID
  * Supports `1024` machines (`2^10`)
  * Use Zookeeper to maintain Machine IDs
* Next 12 bits are an auto-incrementing sequence ID
  * Supports `4096` sequence IDs for a given machine, for a given millisecond
  * When a millisecond ends for a specific machine, the sequence ID resets to `0`
* Final bit is an unset extra bit
* IDs are predictable and non-random
* Twitter uses a custom epoch (and not the Unix epoch) to take advantage of the 69 year space as much as possible
* One approach for calculating a node / machine ID is using the machine's MAC address (which should be unique for every machine)
