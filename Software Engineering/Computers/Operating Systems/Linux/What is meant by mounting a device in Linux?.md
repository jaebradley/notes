# [What is meant by mounting a device in Linux?](https://unix.stackexchange.com/questions/3192/what-is-meant-by-mounting-a-device-in-linux/3194#3194)
* Unix systems have a single directory tree
* All accessible storage must have an associated location in this single directory tree
* Mounting is the act of associating a storage device to a particular location in the directory tree
* When the operating system boots, a specific storage device, usually called the root partition, is associated with the root of the directory tree
* This root partition storage device is mounted on `/`
* Mounting not only associates the device containing the data with a directory, but also with a filesystem driver
* A filesystem driver is a piece of code that understands how the data on the device is organized and presents it as files and directories
* To access files on a CD-ROM, mount the CD-ROM at a location in the directory tree
* `mount /dev/cdrom (your CD-ROM “device”) /media/cdrom (your CD-ROM “device”’s location in the computer’s directory tree)
* If a file at `some directory/some file` on the CD-ROM is now accessible on the operating system at `/media/cdrom/some directory/some file`
