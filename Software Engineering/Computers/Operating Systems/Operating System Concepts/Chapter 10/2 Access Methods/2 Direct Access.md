# 10.2.2 Direct Access

* File made up of fixed-length logical records
  * Read block 10, then block 22, then block 1
* Example is databases - get query, compute which block contains the answer, read that block directly
* Direct access file operations must include the block number as a parameter
  * So `read {some block number}` rather than `read next` like for sequential access
* Request for record `N` -> I/O request for `{record length}` bytes starting at location `{record length}` x `N` within the file
