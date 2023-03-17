# [The tar archive format, its extensions, and why GNU tar extracts in quadratic time](https://mort.coffee/home/tar/)

## The original tar file format

* The only thing in a tar file is a file object
* `512`-byte file object header with information like file path, file mode, file size, file type, etc
* The header is followed by `512`-byte blocks of file contents / payload
* The file type field can be `0` for a normal file, `1` for a "hard link" and `2` for a symbolic link
* So to encode a tar archive with a single file called `"hello.txt"` with the content `"Hello World"`, would need two `512`-byte blocks
  * First block would cotnain the file object header information (`type=0,file_path="./hello.txt",file_size=11`)
  * Second block, `"Hello World"` followed by `501` zero bytes
* Tar files are supposed to end with `1024` (`1KB`) zero-bytes to represent an end-of-file marker
* In original tar format, paths cannot be longer than `100` characters, and files cannot be larger than 8GB

## The UStar file format

* Increases file name length limit to `256` characters
* `prefix` field, allows `256` character file paths, so construct path like `<prefix>/<file_path>` (or `link_path`)
* Added directories and character / block devices

## The pax file format

* Two new file types that can be defined in the header - type `x` and type `g`
  * Records set with `x` only apply to the next file
  * Records set with `g` apply to all the following files
  * With this "extended header" can support file sizes over 8GB
* So previous `hello.txt` example would have the following blocks
  1. Header record, `type=x`, `file_size=30`
  2. File record, `"30 atime=1234\n"`, followed by zeroes to fill rest of `512`-byte block
  3. Header record, `type=0`, `file_path="hello.txt"`, `file_size=11`
  4. File record, `"Hello World"`, followed by `501` zero bytes
* The file path can be provided in four ways
  *  In the `file_path` field
  *  Combination of `prefix` and `file_path` field
  *  Previous file object might've been an `x` type record with a set `path` property
  *  There might've been some `g` type file object earlier in the archive which set a `path` property

## Why GNU tar extracts in quadratic time

* Nothing is stopping an evil archive from containing a file object with `file_path="../hello.txt"`
* Can't just disallow file objects from using `..` as a path component
  * Because could have a symlink with a `file_path=./foo` and a `link_path=..` and then a normal file with `file_path=./foo/hello.txt`
  * We want to allow symlinks which point to their parent directory
  * Could try to figure out whether a symlink will end up pointing to somewhere outside of the extraction directory, complicated when symlinks to symlinks and hard links to symlinks
* When GNU tar encounters a hard link or a symlink with `".."` as a path component in its `link_path`, tar will create a regular file in its place as a placeholder
  * This "delayed link" is added to a linked list data structure
  * Once tar is done extracting the entire archive, it will go through the whole list of delayed links and replace the placeholders with proper links
  * When trying to extract a hard link which does not contain `".."` as a path component in `link_path`, GNU tar will want to create hard link immediately, if possible
  * However, it can't create a hard link if the target is occupied by a placeholder file
  * So every time it wants to create a hard link, tar needs to traverse the linked list of delayed links and see if the target is a delayed link
  * If the target is also a delayed link, the new hard link must also be delayed
* If files alternate between links whose `link_path` contains `".."` as a path component, and hard links which don't contain `".."` as a path component, will continue to add to the delayed link linked list
  * Performance will be quadratic time (`O(n^2)`) as the linked list will be retraversed for every file
* Can use the `--absolute-paths` parameter, which makes tar refuse to extract archive entries whose pathnames contain `".."`, but must trust tar archive as a malicious archive will be able to put files anywhere it wants

