# [Customizing Your Document Routing](https://www.elastic.co/blog/customizing-your-document-routing)

* When a search request is executed on a cluster
  * Request hits a node
  * Node broadcasts request to every shard in the index
  * Each shard performs the search query and responds with results
  * Results are merged on the gateway node, sorted, and returned to the user
* By default, documents are randomly distributed around cluster so ES has to check every shard
* Custom routing (like routing documents based on their user id) tells ES to search only a specific shard which has the potential to increase performance
* However, only way to stop using custom routing is to reindex data (which may be expensive)
* To specify custom routing, update the mapping for a given type and set the `routing` to `true`
  * If you try and index a document without specifying a routing, the operation will fail with an error
  * To index a document with a route, just add a `routing=XYZ` query parameter when `POST`ing to index a document
* To search with routing, append the appropriate `routing=XYZ` query parameter to the `GET` `/_search` request
  * Use a filtered query - if a `match_all` was used then what it's actually asking ES is "Match all documents that reside on shard which `XYZ` routes to
  * The shard probably has other documents other than those that are "orders" (or whatever type is being searched) associated with `XYZ`s
  * Can search multiple custom routes by specifying a comma-separated list like `routing=XYZ,ABC,DEF` - this will search only the specified shard

## Parent / Child / Grandchildren

* Parent / child mappings ensure that all children are routed to the same shard as their parent for performance reasons
  * Internally, ES sets the child's routing value equal to the ID of the parent, ensuring that everyone colocates to the same shard

```bash
curl -XPUT localhost:9200/parentchild/product/Product001 -d '{...}'
curl -XPUT localhost:9200/parentchild/vendors/VendorABC?parent=Product001 -d '{...}'
curl -XPUT localhost:9200/parentchild/vendordetails/LocationXYZ?parent=VendorABC -d '{...}'
```

* The last document (`LocationXYZ`) would have `VendorABC` as its route while `VendorABC` and `Product001` would both have a route of `Product001` - thus, data is not being colocated properly
* In this case, specify a specific `routing` value for the grandchild document - so when `PUT`ting `LocationXYZ` specify `routing=Product001`

```bash
curl -XPUT localhost:9200/parentchild/vendordetails/LocationXYZ?parent=VendorABC&routing=Product001 -d '{...}'
```

## Hotspots

* If there are hotspots that occur due to a specific user having millions of documents and a lot of large users are on the same shard, separate these users onto their own index and set of shards