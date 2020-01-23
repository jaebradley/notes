# [Advanced CLI: Commands You Should Know as a Developer](https://medium.com/better-programming/advanced-cli-commands-you-should-know-as-a-developer-7bc48c752a5e)

* `wget` downloads files served with HTTP(S) or FTP
  * `wget` is non-interactive so it can run in background while user is not logged in so can start retrieving and disconnect from system
* `scp` stands for secure copy
  * Used to copy file from remote server to local directory or vice-versa
  * `scp someuser@somehost.com:/path/to/file.txt /some/local/dir` (and just reverse when pushing local file to server)
* `tar` stands for tape archive and is used to compress / uncompress archive files (i.e. collecting many files into a single file)
  * Create archive file for specific directory - `tar -cvf my-archive.tar /some/directory`
  * Unarchive file - `tar -xvf my-archive.tar -C /some/directory`
* Redirect output using `>`
* `;` is used to execute multiple commands in succession regardless if each previous command succeeded
* `&&` will run subsequent command if prior command was successful
* `||` will run subsequent command if prior command was _not_ successful
