# [Step-By-Step Breakdown Of `/dev/null`](https://medium.com/@codenameyau/step-by-step-breakdown-of-dev-null-a0f516f53158)

* `/dev/null` is a special file called the null device in Unix systems
  * Also known as the bit-bucket or blackhole because it immediately discards anything written to it and only returns an end-of-file `EOF` when read
* As a note, the `$?` symbol is a special variable that always contains the exit status of the previous command
  * An exit code of `0` indicates that the previous command was successful, while anything greater than `0` indicates some type of error code
* To avoid outputting messages to standard error can do something like

```bash
some_invalid_command > /dev/null 2>1
```

* The standard error output is written to `/dev/null` which immediately discards it
* `>&` symbol copies the output of the first file descriptor and redirects the output to the second file descriptor
  * `2` is the file descriptor for standard error and `1` is the file descriptor for standard output
  * So `2>^1` redirects standard error to standard output
* So the final command outputs any errors related to file redirecting to `/dev/null` to standard output

