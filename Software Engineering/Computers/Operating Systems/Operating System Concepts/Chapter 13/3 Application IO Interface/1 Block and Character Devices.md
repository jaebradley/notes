# 13.3.1 Block and Character Devices

* Interface for accessing disk drives and block-oriented devices
* Has a `read` and `write` command
* If the device is a random-access device, expected to have a `seek` command to specify which block to transfer next
