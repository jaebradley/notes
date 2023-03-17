# [User Management in Linux Explained](https://everyday.codes/linux/user-management-in-linux-explained/)

* A multi-user OS means that LInux can be used by multiple users at the same time
* Groups are just groups of users who need access to a certain resource
* `less /etc/passwd` to see all users on your system
* The `passwd` file contains things like username, user id, group id, user's full name, user's home directory, program that runs on login (`nologin` is a program that does nothing)
* `useradd` to add a user and `passwd` to set the password for a user
* `usermod` to modify Linux user - similar arguments to `useradd`
* `userdel` to delete Linux user
* `root` user can read, write, and execute every single file
* `sudo` to run a specific program as `root`
  * `/etc/sudoers` file contains access to `sudo`
  * `%sudo ALL=(ALL:ALL) ALL`
    * Let all users in the `sudo` group on all hosts, run all commands as all users and all groups
* `whoami` to show user and `sudo su` to login as root
* Files have 3 different permissions - permissions for the owner, for the owning group, and everybody else
  * These permissions are organized in `ls`, for example, as `user permissions` `group permissions` `everybody else permissions`
* `chmod` changes permissions on a file
* `chown` changes ownership of a file
* A sticky bit is a parameter that can be set on any directory that prohibits anybody other than the owner from deleting or renaming files in it
  * The sticky bit can be added via `chmod +t someDirectory/`
