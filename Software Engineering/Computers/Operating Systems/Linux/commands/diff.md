# `diff`

## Options
* `-c`: Outputs differing lines with surrounding context
* `-u`: Outputs diffs in a “unified” way
* `-i`: Ignore differences in case when comparing the files
* `-w`: Ignore differences in whitespace when comparing the files
* `-r`: Recursively compare directories and their content
* `-q`: Outputs whether files are different “quietly”
* `-s`: Outputs when files are identical

## Default Output
* `<`: Line prefix indicating that the content is present in the first file but not the second
* `>`: Line prefix indicating that the lines are present in the second file but not the first
* `a(dd)` / `c(hange)` / `d(elete)` symbols indicate how the first file needs to be edited to match the second file
* `2a2,3` -> “In line 2 of the first file, add lines 2 and 3 from the second file”
* `4c5` -> “Change the fourth line in the first file to the fifth line in the second file”
* `---` and `+++` indicate the content file names being compared

## Context Output
* `-`: Content needs to be deleted from first file
* `+`: Content needs to be to added to the first file
* `!`: Content needs to be changed to the corresponding line from the second file
