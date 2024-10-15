# [How To Use ps, kill, and nice to Manage Processes in Linux](https://www.digitalocean.com/community/tutorials/how-to-use-ps-kill-and-nice-to-manage-processes-in-linux)

* `ps` without arguments shows all processes associated with the current user and terminal session
* `ps aux` shows processes owned by all users
* returns total memory and CPU usage, how long they’ve been running, process ID
* `pgrep [search term]` command quickly returns `pid`s matching the search term
* Processes respond to signals
* Signals are a way of telling programs to terminate or modify their behavior
* `kill [PID]` sends the `TERM` signal to the specified process
* The `TERM` signals “politely” tells the process to terminate, allowing the program to perform clean-up operations, exit smoothly
* The `KILL` signal (`kill -KILL [PID]`) is a signal sent to the kernel, which shuts down the process, bypassing programs that ignore signals that are sent to them
* Certain processes are designed to run constantly in the background (sometimes called daemons)
  * These processes will automatically restart when they are given the `HUP` (hang-up) signal
  * Example is Apache webservers which given `kill -HUP pid_of_apache_webserver` will cause the webserver to reload its configuration file and resume serving content
  * Daemons are often-times managed through system services, and it is usually prefereable to restart the service rather than sending a `HUP` signal directly to one running process
* `kill -l` to show all signals
* `pkill` operates on a process name and is similar to `kill -9 'pgrep ping'`
* `killall firefox` would send the `TERM` signal to every instance of a certain process (`firefox`) running on the computer
