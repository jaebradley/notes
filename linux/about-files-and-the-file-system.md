# [About Files and The File System](https://tldp.org/LDP/intro-linux/html/sect_03_01.html)

* On a UNIX system, everything is a file; if something is not a file, it is a process
* Input and output devices (and all devices) are considered to be files
* `ls -F` suffixes file names with a character to indicate file type

## About Partitioning

* By dividing the hard disk into partitions, data can be grouped and separated
* When an accident occurs, only the data in the partition that got the hit will be damaged, while the data on the other partitions will most likely survive
  * If a user creates a script that starts filling up the disk, and the disk only contains one big partition, the entire system will stop functioning if the disk is full
  * If the user stores the data on a separate partition, then only that data will be affected and other system partitions / data partitions will keep functioning
* There are two kinds of major partitions on a Linux system
  * Data partition, which is the normal Linux system data, including the root partition, which contains all the data to start up and run the system

## Mount Points

* All partitions are attached to the file system via a mount point
  * Mount point defines place of a particular data set in the file system
* Given a partition with directories, if we wanted to attach this partition to the filesystem at `/opt/media` would need to ensure that the directory exists on the system
* During system start up, many partitions are mounted (see in `/etc/fstab` file)
* Information about partitions and their mount points can be displayed using the `df` command (`disk full` / `disk free`)

## The File System In Reality

* Every partition has its own file system
  * In a file system, a file is represented by an `inod`, a kind of serial number containing information about the actual data that makes up the file
  * Every partition has its set of `inodes` - throughout a system with multiple partitions, files with the same inode number can exist
