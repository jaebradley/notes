# [Nohup Command in Linux](https://www.digitalocean.com/community/tutorials/nohup-command-in-linux)

* Short for no hang up
* Keeps processes running even after exiting the shell / terminal
  * SIGNUP (Signal Hang Up) is a signal sent to a process upon closing / exiting the terminal program

```bash
# usage
nohup command arguments
nohup options
```

* `nohup ./somescript.sh` will continue running upon exiting the shell / terminal
* Will output to `nohup.out`
* Can redirect output to a different file using standard output redirection like `nohup ./somescript.sh > someoutput.txt`
* `nohup ping google.com &`
  * Start a process in the background that `ping`s `google.com`
  * This process wil run after exiting the shell
  * To check the process when resuming the shell execute the `pgrep` command like `pgrep -a ping`
