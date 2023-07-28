# 13.4.6 I/O Protection

* To prevent users from performing illegal I/O, all I/O instructions are privileged instructions
* Users must execute system calls to request that the OS perform I/O on the user's behalf
* Operating system executes the I/O and then returns control to the user
* Memory-mapped and I/O port memory locations are protected from user access by a memory protection system
