# [Linux File System 101](https://medium.com/swlh/linux-file-system-101-894141449257)

* Everything is a file - hardware devices are files, printer is a file, memory is a file, etc
* The home directory is the only directory that you can write on without the privileged access of root user
  * For other directories, although you can read most of them without root access, can't modify or write on them
* `/bin` (or `binary`) is the directory where Linux keeps its basic programs and applications
  * Binary files are executable files that contain compiled source code (like `ls`, `cat`, `touch`, etc)
* The `$PATH` environment variable contains the paths were users search to look up their binaries and there is no need for root access to search these paths
* `/usr` belongs to user applications while `/bin` belongs to system applications
  * Any application installed here is considered nonessential for basic system operations`
  * Directory is **read-only** and applications should not write to it
  * `/usr/bin` contains binaries like for `firefox`, `vi` etc.
* `/etc` directory is where all system-wide configuration lives, like the configuration for `python`, `python3`, `perl`, etc.
* `/opt` (for `optional`) is where manually installed software resides (like `zoom`, `chrome`)
* `/lib` are programs that are shared among binary applications
  * The binary files inside `bin` and `sbin` use the library files extensively
  * Can see what commands use which library files using the `strace` command (`strace -e open ls` will show what library files `ls` uses)
* `/var` (for `variables`) contains data for "variable" data like system logging, temporary files, etc.
  * `/var/crash` holds inforamtion about every time a process has crashed while `/var/log` has log files for computer / applications
* The `/dev` directory is where device files live
  * These device files allow application programs to interact with your hardware devices
  * _Not_ device drivers, but files that represent each device on the computer and facilitate access to those devices
  * Files can be either character or block files
    * Block device is any device that performs data I/O in units of blocks, like a hard disk
    * Character device is any device which performs data I/O in units of characters, such as standard input/ooutput (like a keyboard or screen)
  * Again, the `/dev` directory has files that allow programs to access the devices (like reading from a hard disk)
* `/proc` (for `process`) is a directory that contains a virtual filesystem (in-memory) that contains a folder for every single process
* Directories in Linux map file names to their address on the hard disk using an index node
  * Index node is a data structure that stores the disk block address and attributes of files (`ls -i` to see index node number for each file and directory)
  * Index node also contains details about the files like their type, their permissions, file owner, file size, timestamp, etc
