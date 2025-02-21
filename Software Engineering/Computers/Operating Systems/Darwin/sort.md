# `sort`

* By default, `sort` uses entire line for comparison
* keys can be extracted from each line of input

## Key definition
* `-k`, `—key=some starting field 1, some optional ending field 2`
* Define a sort key that has the specified starting position and an optional ending field
* This option can be specified many times if there are multiple keys when previous compared keys are equal
* These fields are in the form `m.n`
* The `nth` character from the beginning of the `mth` field

## Options
* `-c`, `—check`: Check that the specified input file is sorted. If the file is not sorted, program outputs error message and exits with code `1`, otherwise returning `0`. `-C` or `—check=silent` will not output an error message.
* `-o`, `-—output=some output file`: Print the output to the specified output file instead of standard output
* `-T`, `—-temporary-directory=some temporary directory`: Default path is `TMPDIR` or `/var/tmp` if `TMPDIR` is not defined
* `-u`, `--unique`: Suppress all lines that have a key that is equal to an already processed ones. When combined with `-c` , will check if there are no lines with duplicate keys.
* `-s`: Stable sorting maintaining the original record order of records that have an equal key
* `-t`, `--field-separater=some character`: Use the specified character as the field separator. Sequence of blank space characters, by default
* `--compress-program=some program`: Use the specified program to compress temporary files. The specified program must compress standard input to standard output when called without arguments
* `--debug`
* `—-parallel`: Maximum number of execution threads, defaulting to the number of CPUs

## Override default ordering rules
* `-b`, `-—ignore-leading-blanks`: Ignore leading blank characters when comparing lines
* `-d`, `--dictionary-order`: Consider only blank spaces and alphanumeric characters in comparisons
* `-f`, `—ignore-case`: Convert all lowercase characters to uppercase equivalents before comparison
* `-i`, `--ignore-nonprinting`: Ignore all non-printable characters
* `-n`, `—numeric-sort`, `—-sort=numeric`: Sort fields numerically by arithmetic value. Optional prefix blank, optional minus sign, can include decimal point and thousand separators
* `-r`, `--reverse`
