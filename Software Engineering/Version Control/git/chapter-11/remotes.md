# Remotes

* Remotes are stored in `.git/config`
* Remotes consist of two parts
  * A URL that refers to the location of the remote
  * A `refspec` that defines how a branch should be mapped from the namespace from one repository to the namespace of another repository

## Git URL
  
* URLs can be in the form of the _Git native protocol_
  * `git://example.com/some/path/to/repo.git`
* They can also be in the form of HTTP/S
  * `http(s)://example.com/path/to/repo.git`

## `refspec`

* Syntax is `[+]source:destination`
  * Optional `+` indicates whether or not fast-forward checking will occur
  * Asterisks are allowed for some form of wildcard matching
  * For pushes, source is local and destination is remote
  * For pulls, source is remote and destination is local
* Default `fetch` `refspec` is `+refs/heads/*:refs/remotes/_some-remote_/*`
  * Basically means, all topic branches in the remote (i.e. **source**) repository will be mapped to `refs/remotes/_some-remote_/` in local cloned repository
  * The `refs/heads/*` part is what references all the topic branches in the remote / **source** repository
  * The `refs/remotes/_some-remote_/` defines the location of the remote topic files in the local repository
* The `refspec` defines the mapping from remote topic branches to local tracking branches
* For `push` operations, the `refspec` typically looks like
  * `+refs/heads/*:refs/heads/*`
  * Basically means, take each topic branch in local **source** repository and place it in matching branch under the `refs/head` namespace in remote repository
