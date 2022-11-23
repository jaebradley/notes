# [How Does NTP Work?](https://sookocheff.com/post/time/how-does-ntp-work/?utm_source=pocket_saves)

## NTP Network Architecture

* Tree structure architecture, where each level (called a stratum) is synchronized with the level above it (other than stratum 0)
* Stratum 0 devices are high-precision timekeeping devices like atomic clocks, GPS, radio clocks
  * Also known as reference clocks
  * NTP servers cannot advertise themselves as stratum 0
* Stratum 1 are computers that are directly attached to stratum 0 devices
  * Synchronized to within a few microseconds of their attached devices
  * Stratum 1 servers may peer with other stratum 1 servers for sanity checking
  * Stratum 1 are also known as primary time servers
* Stratum 2 are computers that are synchronized over a network to stratum 1 servers
 * Stratum 2 computers query several stratum 1 servers and selects the most accurate time representation from them
* Hierarchy continues until stratum 15

## The Local Clock Model

* Quartz clocks work by applying voltage to the quartz crystal
 * When a voltage is applied, the crystal changes shape
 * When the voltage is removed, the crystal returns to its original shape
 * This fluctuation in shape happens at a stable frequency (accuracy within a few milliseconds per day)
* Local computer system uses these quartz clock oscillations to increment the logical clock of the computer
* NTP can adjust inaccuries of quartz clocks
 * NTP client receives time data from one or more connected servers and uses that time data to compute a phase or frequency correction to apply to the local clock
 * If the time correction is small, the correction is applied gradually (slewing) in order to avoid clock jumps
 * If the local clock is off by a large amount, the adjustment may be applied all at once
 
 ### NTP's Clock Discipline Algorithm
 
 * NTP client is connected to X number of NTP servers
 * There are clock filters which select the best time offset from the previous eight time samples received by the client from a given NTP server
 * Additional selection and clustering algorithms narrow the dataset by pruning the outliers
 * An algorithm combines the pruned result set, applying a weighted average of the timestamps
