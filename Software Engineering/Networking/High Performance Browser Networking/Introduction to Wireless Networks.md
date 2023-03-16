# Introduction to Wireless Networks

## Performance Fundamentals of Wireless Networks

* Maximum channel capacity (measured in bits per second) is the product of the avaialble bandwidth (measured in hertz) multiplied by the logarithm of (1 + Signal / Noise). Signal and Noise are both measured in watts
* The fundamental constraints on achievable data rates are the amount of available bandwidth and the signal power between the receiver and the sender

## Bandwidth

* Both the sender and receiver must agree up-front on the specific frequency range over which the communication will occur (802.11b and 802.11g standards use 2.4-2.5GHZ band across all WiFi devices)
* Governments define frequency and allocation
  * Some wireless technologies may work in one part of the world
* The overall channel bitrate is directly proportional to the assigned range
  * All else equal, a doubling in available frequency range will double the data rate
  * So going from 20 to 40MHz of bandwidth can double the channel data rate
* Low-frequency signals travel farther and cover large areas, but at the cost of requiring larger antennas and having more clients competing for access
* High-frequency signals can transfer more data but won't travel as far

## Signal Power

* By definition, all radio communication is done over a shared medium, which means that other devices may generate unwanted interference
  * A microwave oven operating at 2.5GHz may overlap with the requency range used by WiFi
* Analogy using small room and talking with somebody 20 feet away
  * If nobody else is present, can talk at a normal volume
  * If a few dozen people are added to the room, now have to speak louder
  * But by speaking louder, it would be noisier for everybody and they would start speaking louder as well
  * Everybody ends up only being able to communicate from a few feet away from each other
* Near-far problem is when a receiver captures a strong signal and makes it impossible for the receiver to detect a weaker signal, "crowding out" the weaker signal
  * From the analogy, near-far problem happens when at least one loud speaker besides you can block out weaker signals from farther away
* Cell-breathing occurs when the coverage area or distance of the signal expands and shrinks based on the cumulative noise and interference levels
  * From the analogy, the more conversations around you, the higher the interference and the smaller the range from which you can discern a useful signal
