# [Columnar Storage is Normalization](https://buttondown.com/jaffray/archive/columnar-storage-is-normalization/)
* For a relational database (row-oriented data), adding or looking up wide rows don’t impact the number of disk pages that need to be accessed as the column data is colocated on disk
* It also means that looking up data for a single column across all rows, especially if the rows are wide (I.e. many columns) means reading a lot of data that is not relevant
* Column-oriented data orders the data by column
```json
data = {
    "name": [
        "Smudge",
        "Sissel",
        "Hamlet"
    ],
    "colour": [
        "black",
        "grey",
        "black"
    ],
}
```
* Optimized for reading *only* data from certain columns
* Modifying data or reading a specific row of data is harder
* Columnar data stores are a very extreme type of database normalization
* Instead of a relational table with an `id`, `name`, and `age` columns, imagine a `name` table and an `age` table with that same `id` primary key
  * In a columnar store, the `id` primary key is the index of the column value entry
  * Building the relational table (row-based) view from these disparate table involves joining all the tables together on this primary key / index