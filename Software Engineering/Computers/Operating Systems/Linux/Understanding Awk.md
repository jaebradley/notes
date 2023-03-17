# [Understanding Awk](https://earthly.dev/blog/awk-examples/)

* By default, `awk` expects to receive its input on standard input and output its results to standard output
* `awk` calls columns "fields" and each line a record
  * `echo "one two three" | awk '{ print $1 }' - will print `one`
  * `awk '{ print $0 }'` prints the entire line
* Can print multiple columns like `awk '{ print $1, $2 }'`
* Can access last column using `$NF` which stands for "Number of Fields"
* By default, `awk` assumes that the fields in a record are whitespace delimited
  * To change this field separator, use `awk -F` like `awk -F '\t'` to change the field separator to be a tab
* Can also work backwards from the last position by subtracting from `NF`
  * `awk -F '\t' '{ print $NF "\t" $(NF-2})' file.tsv` - outputs the last column and the third-to-last column
* `awk` also provides `NR` which stands for "Number of Records" (so far) which is useful to print line numbers
  * `awk -F '\t' '{ print NR " " $(NF -2) }' file.tsv` - the beginning of each line will be the line number / record count, followed by a space, and then the third-to-last column value

## Pattern Matching

* `awk '/bb/ { print $2 }'` - would find records that have a `bb` and print the 2nd column from these records
* Can filter records that have exactly one value in a column like `awk -F '\t' '$4 == "1234" { print $6 }' file.tsv`
  * This would filter records that exactly match the value in the fourth column
* Can also match within a specific field like `awk '$4~/hello { print "This field contains hello", $4 }'`

## `printf`

* `print $1 "\t" $2 "\t" $3` becomes `printf "%s \t %s \t %s", $1, $2, $3`

## `BEGIN` and `END`

* `awk -F '\t' '{ total = total + $8; print total }' file.tsv`
  * 8th column has numbers
  * Outputs running total on each line
* To calculate average, can do

```bash
awk -F '\t' '
  { total = total + $8 }
END { print "Average:", total/NR }
' file.tsv
```

* Also a `BEGIN` pattern that allows running actions before processing records

## Scripting

* Can embed `awk` into a Bash script

```bash
# average file
exec awk -F '\t' '
  { total = total + $8 }
END { print "Average book review is", total/NR, "stars" }
' $1
```

* This script can be used like `average bookreviews.tsv`
* Can call `awk` from a file
  * `awk -f file.awk input`

## Associative Array

* `arr["key1"] = "one"`

```awk
for (i in arr) {
  print $i, arr[i]
}
```

```awk
BEGIN { FS = "\t" }
$6~/\(The Lord of the Rings, Book .\)$/ {  # <-- changed this line
  title[$6]=$6
  count[$6]= count[$6] + 1
  total[$6]= total[$6] + $8
}
END { 
    for (i in title) {
        printf "---------------------------------------\n"
        printf "%s\n", title[i]
        printf "---------------------------------------\n"
        printf "Ave: %.2f\t Count: %s \n\n", total[i]/count[i], count[i]  
    }
}
```

## If/Else

```awk
echo "1\n 2\n 3\n 4\n 5\n 6" | awk '{
        if (NR % 2) 
            print "odd"
        else
            print $0
        }'
```
