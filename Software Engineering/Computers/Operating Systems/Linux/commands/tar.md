# `tar`

* Use `tar` to combine multiple files and directories into a single archive file - a “tarball”
* Can extract files from an existing archive, making it easier to unpack compressed files
* List the contents of an archive without extracting the files
* Supports various compression algorithms (gzip) to reduce size of archive file
* The `-f`/`—file` option allows specifying the archive file name

## Creation

* `tar -cf archive.tar directory/` creates uncompressed archive file named `archive.tar` containing the files in the `directory`
* By default, will recurse through directories unless the `—no-recursion` option is specified
* `tar -czf archive.tar.gz directory/` creates gzip-compressed archive file named `archive.tar.gz`
* To add a file to an existing archive there is an `—append` / `-r` option like `tar -rvf archive.tar somefile`
* To remove a file from an existing archive use the `—delete` operation like `tar —delete -f archive.tar somefile`

## Extraction

* `tar -xf archive.tar` extract all files from the uncompressed archive `archive.tar`
* `tar  -xzf archive.tar.gz` extracts all files from gzip-compressed archive `archive.tar.gz`
* To extract files to a specific directory use the `-C` option like `tar -xf archive.tar -C /some/directory`
* `tar -tf archive.tar` lists the contents of the uncompressed archive `archive.tar` without extracting
* `tar -tvf archive.tar` lists the contents of the uncompressed archive `archive.tar` with verbose output
* Can use wildcards to extract files matching a certain pattern like `tar -xf archive.tar —wildcards ‘*.js’`
