# Column Family

## [What is column family in gcp bigtable and how is data stored in bigtable?](https://stackoverflow.com/questions/70400024/what-is-column-family-in-gcp-bigtable-and-how-is-data-stored-in-bigtable)

* Column family in Bigtable refers to sets of columns that are related to one another
* These column sets help Bigtable organize and store data

```
          |   Column Family 1   |   Column Family 2  |
          | Column 1 | Column 2 | Column 1 | Column 2|
Row Key 1 | Value 1  |  Value 2 |          | Value 3 |
Row Key 2 | Value 4  |  Value 5 | Value 6  |         |

Row Key 1
  cf1:c1
    "Value 1"
  cf1:c2
    "Value 2"
  cf2:c2
    "Value 3"

Row Key 2
  cf1:c1
    "Value 4"
  cf1:c2
    "Value 5"
  cf2:c1
    "Value 6"
```

## [Why do we need column families in Hbase?](https://stackoverflow.com/questions/64944559/why-do-we-need-column-families-in-hbase)

* Column families allow ways to create structure within the table to optimize performance based on access patterns
* Column families are stored on different files
* HBase stores all values for a given row in the same Region
  * In other words, the separate files for a column family are stored on the same Region Server
* If data was stored on different tables (instead of using a column family)
  * Now parts of the same "row" would live in different HBase Regions
  * Accessing these different HBase Regions would incur the cost of looking up different Region Servers on the cluster

## [Column-family concept and data model](https://stackoverflow.com/questions/3245267/column-family-concept-and-data-model)

* One mental model for thinking about a column family is that it's a Map of a Map of Key/Value pairs
  * Tabular equivalent is Table (as the outer Map) -> Row (outer Map Key) -> Values (inner Map)
  * The inner Map values in the tabular equivalent looks like "Column 1 -> Value 1", "Column 2 -> Value 2", etc
* Helpful to think of the columns within a column family as "attributes"
  * Rows are then "attribute sets"
  * And column families are "attribute families"
* Internally, data for each column family is stored together
  * Rows are stored sequentially
  * Columns and their values are stored sequentially (in the order they are defined)
  * `row key 1 -> column 1 / value 1, column 2 / value 2, column 3 / value 3` etc
