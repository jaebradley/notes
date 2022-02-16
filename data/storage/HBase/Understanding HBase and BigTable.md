# [Understanding HBase and BigTable](https://dzone.com/articles/understanding-hbase-and-bigtab)

## multidimensional

* Example JSON looks like

```json
{
  "1" : {
    "A" : "x",
    "B" : "z"
  },
  "aaaaa" : {
    "A" : "y",
    "B" : "w"
  },
  "aaaab" : {
    "A" : "world",
    "B" : "ocean"
  },
  "xyz" : {
    "A" : "hello",
    "B" : "there"
  },
  "zzzzz" : {
    "A" : "woot",
    "B" : "1337"
  }
}
```

* Each top-level key points to a map with exactly two keys (`A` and `B`)
* Think of top-level key/value pair as a "row" (so key `1` mapping to the underlying "object" is a row)
* `A` and `B` key/value pairs represent "column families"
* Column families are difficult or impossible to modify later
* Nested columns within a column family

```json
{
// ...
  "aaaaa" : {
    "A" : {
      "foo" : "y",
      "bar" : "d"
    },
    "B" : {
      "" : "w"
    }
  },
  "aaaab" : {
    "A" : {
      "foo" : "world",
      "bar" : "domination"
    },
    "B" : {
      "" : "ocean"
    }
  },
// ...
}
```

* The `A` column family has two columns `foo` and `bar` and the `B` column family has just one column whose qualifier is the empty string
* When asking HBase/BigTable for data, the full column name would be `A:foo` or `A:bar`
* All data is versioned using an integer timestamp, so this versioned data would look like

```json
{
// ...
  "aaaaa" : {
    "A" : {
      "foo" : {
        15 : "y",
        4 : "m"
      },
      "bar" : {
        15 : "d",
      }
    },
    "B" : {
      "" : {
        6 : "w"
        3 : "o"
        1 : "w"
      }
    }
  },
// ...
}
```

* Each column family will have rules around how many versions of a given cell to keep
* Applications will simply ask for a given cell's data, without specifying a timestamp and HBase / BigTable will return the most recent version
* If an application asks for a given row at a given timestamp, HBase will return cell data where the timestamp is less than or equal to the one provided
