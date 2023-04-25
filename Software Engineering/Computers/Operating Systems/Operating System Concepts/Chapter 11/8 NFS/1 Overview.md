# 11.8.1 Overview

* NFS a set of interconnected workstations as a set of independent machines with independent file systems
* A machine can be a client and a server
* Sharing of a remote file system affects only the client machine and no other machine
* A client of a machine must execute a mount operation to enable access to a remote directory
  * Mount remote directory over a directory of a local file system
  * Once the mount operation is completed, the mounted directory looks like a subtree of the existing local file system
  * The mounted directory replaces the subtree descending from the local directory
* Any file system / any directory within a file system can be mounted remotely on top of any local directory
* If a shared file system is mounted over a user's home directories on all machines in a network, the user can log into any workstation and get his home environment
