# [Increase Developer Productivity with Unix Bash Command One-Liners](https://itnext.io/increase-developer-productivity-with-unix-bash-command-one-liners-2973bccd7600)

* `echo "find . -name secret_file" > find_secret.sh` - save quoted text to file `find_secret.sh`
* `chmod +x some_script.sh` - make script executable
* If you alias `find . -name` to something like `alias findn='find . -name'` then you can just find files like `findn "secret_file"`
* `find . -type f -size +500M` - find files in cwd that are larger than `500MB`
* `grep -lir "some text" *` - search for text within all files, recursively
, ignoring case, and outputting files with matches
* `lsof / | awk '{ if($7 > 1048576) print $7/1048576 "MB" " " $9 " " $1 }' | sort -n -u | tail` - show 10 largest open files
* `tr -dc 'a-zA-Z0-9~!@#$%^&*_()+}{?></";.,[]=-' < /dev/urandom | fold -w 32 | head -n 1` - generate random 32 character password
* `!!` - runs the last command
* `!ln` - runs last command beginning with ln
* `rm 'find . -name "*.html"'` - use `rm` to remove files that are output of all found `html` files
  * Can also do `find . -name "*.html" -delete` using `delete` flag on `find`
* `sed "/{characters}/Id" some_file - remove lines that contain the characters (case-insensitive)
  * Remove `I` flag for case-sensitivity
* `grep -C 3 'secret' ./some_file --color` - print lines container 'secret' with 3 lines before and after with color
* `sed -i '/^$/d'` - remove empty lines
