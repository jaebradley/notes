# Optimizing Application Delivery

## Cache Resources on the Client

* `Cache-Control` header can specify the cache lifetime (i.e. max-age) of the resource
* `Last-Modified` and `ETag` headers provide validation mechanisms
* Need to specify both the cache lifetime and the validation method

## Compress Transferred Data

* The size of text-based assets can be reduced by 60-80% on average when compressed with gzip
* Images often carry a lot of metadata that can be stripped
  * Images should be sized to their display width to minimize transferred bytes
  * Images can be comperssed with different lossy and lossless formats

