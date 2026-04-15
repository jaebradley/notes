# [Shard](https://vitess.io/docs/23.0/concepts/shard/)
* Shard is a subset of a keyspace
* Keyspace always contains one or more shards
* Shard typically contains one MySQL primary and many MySQL replicas

## Shard Naming
* Represent range of unsigned integer space, left inclusive and right exclusive
* Notation is hexadecimal
* Left justified, right-padded with zeros
* `-80` = `00-80` = `0000-8000` = … `0000000000000000-8000000000000000`
* `80-` is not the same as `80-FF`
  * `80-FF` = `8000-FF00`, which means `FFFF` is out of the `80-FF` range
  *  `80-` means anything greater than or equal to `0x80`
* Left-justified approach means that keyspace IDs can be of arbitrary length
  * Most-significant bits are the ones on the left
  * MD5 hashes produce 16 bytes and these can be used as keyspace IDs
