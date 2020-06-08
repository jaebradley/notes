[How A Bitmap Index Works](https://richardstartin.github.io/posts/how-a-bitmap-index-works)

* Imagine a table of record index (like a pk id), country, and sector

```bash
Index | Country | Sector
  0   |   GB    | Financials
  1   |   DE    | Manufacturing
  2   |   FR    | Agriculturals
  3   |   FR    | Financials
  4   |   GB    | Energies
```

* And then the bitmaps would look like

```bash
# Country Bitmap Index

Value | Indices | Bitmap
  GB  |   0,4   | 10001
  DE  |    1    | 10
  FR  |   2, 3  | 1100

# Sector Bitmap Index

Value         | Indices | Bitmap
Financials    |  0, 3   | 1001
Manufacturing |   1     | 10
Agriculturals |   2     | 100
Energies      |   4     | 10000
```

* The bitmap value is `1` if the record index at that index (from right to left) has the value and `0` if not
* The number of bitmaps for an attribute is the attribute's distinct count
  * So the fact that there are three bitmaps for country, mean that there are three distinct values for country
* The length of the bitmaps depend on the sort order of the record index attribute (id)
  * A bitmap on the record index attribute (id) itself would be as large as the data itself

## Query Evaluation

* Bitmaps can be composed efficiently to performa query evaluation

```sql
SELECT *
FROM records
WHERE country = "GB" OR country = "FR"
```

* Access the country index, read bitmaps for values `FR` and `GB` (`1100` and `10001` respectively)
* Apply a bitwise logical `OR` to get new bitmap (`11101`) which means that all but the second row has a country that's `GB` or `FR`

```sql
SELECT *
FROM records
WHERE country = "GB" AND sector = "Energies"
```

* Access the country index and read bitmap value fo `GB` (`10001`)
* Access the sector index and read bitmap value for `Energies` (`10000`)
* Apply a bitwise logical `AND` to get new bitmap (`10000`) which means that only the fifth row has a country that's `GB` and a sector that's `Energies`

```sql
SELECT *
FROM records
WHERE country <> "GB"
```

* Access the country index and read bitmap value for `GB` (`10001`)
* Negate the bitmap (`01110`) - this means that the second, third, and fourth rows are the rows where the country is not `GB`

```sql
SELECT country, count(*)
FROM records
GROUP BY country
```

* Iterate over keys in country index and count bits in each bitmap
  * `GB` has two bits, `DE` has a single bit, `FR` has two bits

```sql
SELECT country, count(*)
FROM records
WHERE sector <> "Financials"
GROUP BY country
```

* Access sector index and read bitmap for "Financials" (`1001`)
* Negate the bitmap (`10110`) and call it `without_financials`
* Access country index and iterate over keys
  * Intersect each bitmap with `without_financials` and then count bits in resultant bitmap
  * `GB` bitmap is `10001` so intersection is `10000` (count is `1`)
  * `DE` bitmap is `10` so intersection is `00010` (count is `1`)
  * `FR` bitmap is `1100` so intersection is `00100` (count is `1`)
