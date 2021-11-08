# [5 Unix Terminal Tips To Boost Your Coding Speed](https://betterprogramming.pub/5-unix-terminal-tips-to-boost-your-coding-speed-c85f880606ed)

* The ampersand character (`&`) executes a task in the background
  * Not appropriate for long-running tasks that print to console, because the shell blocks when the process prints text
  * Instead, detach the output from the shell by outputting to `/dev/null` like `some_script.sh &> /dev/null &`
* Check the status of background tasks with the built-in `jobs` command
* Spawn a process via the `nohup` command if the process needs to be active even after closing the terminal
