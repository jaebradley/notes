# [3 Uncommon Bash Tricks That You Should Know](https://medium.com/better-programming/3-uncommon-bash-tricks-that-you-should-know-c0fc988065c7)

* Instead of `mv README.txt README.md` do `mv `README.{txt,md}`
  * Parameter expansion creates one argument for each element inside the curly braces, separated by a comma
  * Can also do this with a sequence of numbers like `{0..2}` which can be used like `mkdir data{0..2}` to make directories named `data0`, and `data1`, and `data2`
* Can access the last argument using `$_`
  * `mkdir temp; cd temp` can be shortened to `mkdir temp; cd $_`
* Let's say there's a typo and `ssh wrong_value@some_host` should be `ssh correct_value@some_host`
  * Can do `^wrong_value^correct_value` and this will change execute the last command but with the value substituted
  * Similar to `!!:s/old/new` where `!!` gets the lat command and the `:s` does the substitution
