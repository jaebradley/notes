# `du`
* Displays file system block usage for each file argument
* If no file is specified, the block usage of the hierarchy rooted in the current directory is displayed

## Options
* `-I [mask]`: ignore files/directories matching mask
* `-L`: Symbolic links are followed
* `-P`: No symbolic links are followed
  * Default behavior
* `-c`: Display a grand total
* `-d [depth]`: Display an entry for all files/directories at the specified depth
* `-t [threshold]`: Only display entries whose size exceeds the threshold
* `-h`: Human-readable output

## Why would `df` differ from `du`
* `df` gets data from the filesystem’s primary superblock
  * `df` reports only on the filesystem mount point level
  * So `df /mountpoint` and `df /mountpoint/subdirectory` would  produce the same results
* `df` includes open files
  * In memory, but not on disk
  * Data/index files (2-5% of the filesystem)
  * Unnamed files
* `du` reports at the “object” level, rather than at the filesystem mount point level
* `du` does not count data/index files or open files
* `du` does not rely on block size to determine file size
  * Default filesystem block size is `8kb`
  * Creating a `1kb` file and write to an empty `8kb` block
  * `du` will report the file as `1kb` in size and *not* the minimum size filesystem block size
  
## Resources
* [Linux commands: du and the options you should be using](https://www.redhat.com/en/blog/du-command-options)
* [Why DU And DF Display Different Values On Linux And Unix](https://linuxshellaccount.blogspot.com/2008/12/why-du-and-df-display-different-values.html)
