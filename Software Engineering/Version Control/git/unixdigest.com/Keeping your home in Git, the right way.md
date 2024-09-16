# [Keeping your home in Git, the right way](https://www.unixdigest.com/tutorials/keeping-your-home-in-git.html)

* Makes no sense to keep entire `$HOME` in a git repository
* Balance not having to maintain a large `.gitignore` (i.e. remembering to update the `.gitignore` each time an unimportant file gets added to `$HOME`) with ensuring all relevant files are tracked
* Start by creating a `.gitignore` in the `$HOME` directory with an `*`, which means that everything should be ignored
* Manually `git add` files from `$HOME` to git with the `-f` (force) option
  * Benefit is that now the `.gitignore` is an allow-list and only stuff that is needed are added
  * No interactions with repositories (`$HOME` `.gitignore` does not affect any repositories in `$HOME` and any `.gitignore`s in those repositories do not impact `$HOME` `.gitignore`)
  * If you're using a bare repository, can easily `git push` / `git pull` changes to that repository without affecting any other files in `$HOME`

## Cloning `$HOME` directory from bare repository server to new server

* SSH into server and create the remote bare repository in the server

```bash
# Assuming SSHed into server
mkdir -p SomeUserName/repo/home.git
cd SomeUserName/repo/home.git
git init --bare
```

* On a new box, to pull / clone from `home.git`

```bash
git init
git remote add origin ssh://SomerUserName@SomeServer.com/home/foo/repo/home.git
git fetch git reset --hard origin/main
```