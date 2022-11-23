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
