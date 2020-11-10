# [How Git Really Works](https://medium.com/better-programming/a-short-journey-to-git-internals-fc9d11dd80b7)

* Git is simply a key-value database - you can insert any kind of content into a Git repository, which Git will give you back a unique identifier (a key) that you can use to retrieve the content
* `echo hello | git hash-object --stdin -w` - now the value "hello" is in the Git data store and the returned hash is the key in which to look up "hello"
* `git cat-file -p <the-returned-hash>` will output "hello"
* Can see all objects in the Git object database by looking at `.git/objects`
* Each directory has a hash suffix name
* The tree object (vs. blob objects which we were looking at previously) contains entries where each entry is the `SHA-1` of the blob or subtree with it's associated mode, type, and filename
* In the examples in the article, the "reverse" process is used where values are written directly to the Git datastore
  * In order to copy the files from the index to the working tree (i.e. checking out the files) execute `git checkout-index 0 -a` where the `-a` stands for all files
