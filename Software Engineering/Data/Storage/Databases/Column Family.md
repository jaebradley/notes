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

* Column families allow ways to create  structure within the table to optimize performance based on access patterns
* Column families are stored on different files
* HBase stores all volues for a given row in the same Region
  * In other words, the separate files for a column family are stored on the same Region Server
* If data was stored on different tables (instead of using a column family)
  * Now parts of the same "row" would live in different HBase Regions
  * Accessing these different HBase Regions would incur the cost of looking up different Region Servers on the cluster
