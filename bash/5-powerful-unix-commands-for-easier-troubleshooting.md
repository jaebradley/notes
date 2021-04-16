# [5 Powerful Unix Commands for Easier Troubleshooting](https://betterprogramming.pub/5-powerful-unix-commands-for-easier-troubleshooting-dd619d5e173a)

* `lsof` command (list open files)
  * Without args, lists all open files belonging to all active processes
  * `lsof -u someuser`
  * `lsof -i :8090` (all running processes on a specific port)
    * `lsof -i :8090-9090` - query ports within a specified range
* `ps` is a useful command to display information about running processes
  * with `-ef` options it displays all processes (`e`) and provides detailed information about the processes (`f`)
  * `-a` displays processes for all users
  * `-u` displays data in a user-oriented format
  * `-x` shows processes running in the background
  * `--sort=-%mem` sorts by memory
* `less +4 -N` shows line numbers and jumps to line `4` in the file
