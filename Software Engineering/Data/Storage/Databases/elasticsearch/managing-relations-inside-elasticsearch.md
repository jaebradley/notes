# [Managing Relations Inside Elasticsearch](https://www.elastic.co/blog/managing-relations-inside-elasticsearch)

## Inner Objects

* JSON objects inside parent document
* Issues when relationship is more than one-to-one

```json
{
  "name" : "Zach",
  "car": [
    {
      "make": "Saturn",
      "model": "SL"
    },
    {
      "make": "Subaru",
      "model": "Imprezza"
    }
  ]
}
```

* Query like `car.make = Saturn AND car.model = Imprezza` will return `Zach`'s document because Elasticsearch flattens inner objects into a single object

```json
{
  "name": "Zach",
  "car.make": [ "Saturn", "Subaru" ],
  "car.model": [ "SL", "Imprezza" ]
}
```

## Nested Types

* Nested documents look identical to inner objects at the document level
  * When the object mapping is defined, nested types must be explicitly delared (inner boject types are automatically detected)

```json
{
  "person": {
    "properties: {
      "name": {
        "type": "string"
      },
      "car": {
        "type": "nested"
      }
    }
  }
}
```

* When a nested document is created, Elasticsearch indexes two separate documents (the root object and the nested object) and then relates the two internally
  * Both documents are stored in the same Lucene block on the same Shard, so read performance is fast
* Can only access the nested documents using a special `nested query`
* Updating one field in the nested doc will force a reindex of _the entire document_
  * Elasticsearch will mark the old document as deleted, update the field, and then reindex everything into a new Lucene block
  
## Parent / Child Types

* Use the `_parent` proeprty to map a child reference to a parent reference
* The mappings are defined outside the parent mapping definition

```json
{
  "mappings": {
    "person": {
      "name": {
        "type": "string"
      }
    }
  }
}

{
  "homes": {
    "_parent": {
      "type": "person"
    },
    "state": {
      "type": "string"
    }
  },
}
```

* When indexing a child, need to specify what the parent document is

```curl
curl -XPOST localhost:9200/homes?parent=zach -d

{
  "state": "Ohio"
}
```

* Can follow-up with request to add other state, and now both documents are associated with `zach`
* Parent and children types can be queried individually, and can avoid using the "nested query"
* Parent/Child is slightly slower than Nested since they are not colocated on the same Lucene block
  * There is also slighlty more memory involved since Elasticsearch needs to keep an in-memory "join table" which manages the relationship between the two entities
  * Child documents are still routed to the same shard as the parent, so they still benefit from shard-level caches and memory filtering
