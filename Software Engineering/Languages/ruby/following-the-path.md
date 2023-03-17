# [Following the Path](https://robots.thoughtbot.com/following-the-path)

* `$LOAD_PATH` is a global variable that represents an array of string paths
* `require "user"` - Ruby looks for the file `user.rb` in all the `$LOAD_PATH` paths
  * Ruby will load the first one it finds
* Same logic applies to complex paths like `require "/api/client"` - will look for `/api/client.rb` in all paths

## `UNIX`

* Unix systems take similar approach with the `$PATH` environment variable
* `$PATH` is a list of paths that are `:`-separated
* When a command is typed into the shell, all `$PATH` locations are investigated to find matching executable
  * The first matching executable will be evaluated
* Can find path for executable using `which`
  * Useful when multiple versions of executable with same name is installed
    * `psql` from Homebrew Postgres installation and `psql` from `Postgres.app`
    * `which psql` would tell you which `psql` is being used (and the path to the `psql` that's being executed)
    * `-a` flag will show locations for all matching executables
