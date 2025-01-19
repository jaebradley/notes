# gzip

## Compression

* `gzip file.txt`: replaces `file.txt` with compressed version named `file.txt.gz`
* `-k` option that leaves the original file
* The `-c` option also keeps the file and writes the compressed output to standard output
* Can compress multiple files at the same time
* `-r` option compresses all files in a directory
* Compression levels from `-1` to `-9` with `-9` being the most compressed, but slowest
* `gzip` can also read from standard input via a pipe

## Decompression

* To decompress a gzipped file use the `-d` option like `gzip -d file.gz`
* File that is being decompressed must have a `.gz` file suffix
* The `gunzip` command is an alias for `gzip -d`
* `-k`/`-c` options also keeps the compressed file when decompressing
* The `-l` option shows statistics about the given compressed files, like the compression ratio, number of compressed / uncompressed bytes
* The `-v` option displays even more information, like the compression method and date/time of the compression
* To compress multiple files or directories into a single compressed file, create a Tar archive using the `tar` command and then compress the `.tar` file using `gzip`, resulting in a `.tar.gz` or `.tgz` suffix
