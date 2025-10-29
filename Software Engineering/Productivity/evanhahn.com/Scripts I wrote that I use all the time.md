# [Scripts I wrote that I use all the time](https://evanhahn.com/scripts-i-wrote-that-i-use-all-the-time/)
* `pbpaste` is clipboard paste utility for MacOS (opposite of `pbcopy`)
* Script to `mkdir {xyz} && cd {xyz}`
* Using `trash` vs. `rm` instead of accidentally deleting files
* Command `url "${some url}"` that parses a URL into its parts
  * Script location: [https://codeberg.org/EvanHahn/dotfiles/src/commit/843b9ee13d949d346a4a73ccee2a99351aed285b/home/bin/bin/url](https://codeberg.org/EvanHahn/dotfiles/src/commit/843b9ee13d949d346a4a73ccee2a99351aed285b/home/bin/bin/url) 
* `line` utility that prints line X from standard input
* `upper` and `lower` commands that wrap `tr [:upper:]/[:lower:]`
* `timer` command to start timer in some specified time length
* Print current date in ISO format
  * Wraps `date` command: [https://codeberg.org/EvanHahn/dotfiles/src/commit/843b9ee13d949d346a4a73ccee2a99351aed285b/home/bin/bin/hoy](https://codeberg.org/EvanHahn/dotfiles/src/commit/843b9ee13d949d346a4a73ccee2a99351aed285b/home/bin/bin/hoy) 
* `rn` (right now) command that prints current date + time with calendar view
  * [https://codeberg.org/EvanHahn/dotfiles/src/commit/843b9ee13d949d346a4a73ccee2a99351aed285b/home/bin/bin/rn](https://codeberg.org/EvanHahn/dotfiles/src/commit/843b9ee13d949d346a4a73ccee2a99351aed285b/home/bin/bin/rn) 
* `each` wraps `xargs` + `find ... --exec`
  * `ls | each 'du-h {}'` runs `du -h` on every file in the directory
* There's also a process ending command that wraps `kill`
  * `kill -15`, then `kill -2`, then `kill -1`
* `httpstatus` cli, maybe there a way to scrape the data from some source and then lookup by status prefix?
* `notify` to send operating system notifications
  * This command adds additional syntax: [https://codeberg.org/EvanHahn/dotfiles/src/commit/843b9ee13d949d346a4a73ccee2a99351aed285b/home/bin/bin/notify](https://codeberg.org/EvanHahn/dotfiles/src/commit/843b9ee13d949d346a4a73ccee2a99351aed285b/home/bin/bin/notify) that adds the current application
