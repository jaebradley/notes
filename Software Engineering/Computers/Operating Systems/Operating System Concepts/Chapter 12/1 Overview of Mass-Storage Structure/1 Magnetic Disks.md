# 12.1.1 Magnetic Disks

* Platter is covered in magnetic material
* Information is stored magnetically on the platters
* Read-write head operates just above the surface of each platter
* An arm moves all the heads as a unit
* Platter surface is comprised of concentric circular tracks
  * These tracks are subdivided into sectors
  * The set of tracks across each platter & read-write head combination is called a cylinder
* Disk speed is comprised of two parts
  * Transfer rate is the rate at which data flows between the drive and the computer
  * Positioning time is the time it takes to move the disk arm to the desired cylinder (seek time) + the time necessary for the desired sector to rotate to the disk head (rotational latency)
* The disk head flies on an extremely thin cushion of air
  * If head makes contact with the disk surface and damage the magnetic surface
  * Usually this head crash cannot be repaired and the entire disk must be replaced
* Disk drive is attached to a computer by a set of wires called an I/O bus
  * Data transfers on a bus are carried out by special electonic processors called controllers
  * Controller at the computer end of the bus is the host controller
  * Disk controller is built into each disk drive
* Computer places a command into the host controller using memory-mapped I/O ports
  * Host controller sends the command via messages to the disk controller
  * Disk controller operates the disk-drive hardware to carry out the command
  * Disk controllers have a cache so data transfer goes from disk -> cache -> host controller
