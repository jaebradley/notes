# Commodity Hardware Today

* Over the years, personal computers and smaller servers standardized on a chipset with two parts - Northbridge and Southbridge
  * All CPUs are connected via a common bus (the Front Side Bus) to the Northbridge
  * The Northbridge contains the memory controller (in addition to other things)
  * The Northbridge must communicate with the Southbridge
  * The Southbridge is referred to as the I/O bridge, handles communication with devices through a variety of different buses
* All data communication from one CPU to another, must travel over the same bus used to communicate with the Northbridge
* All communication with RAM must pass through the Northbridge
* The RAM has only a single port
* With limited bandwidth available, it is important for performance to schedule memory access in ways that minimize delays
  * As we will see, processors are much faster and must wait to access memory, despite the use of CPU caches
  * If multiple hyper-threads, cores, or processors access memory at the same time, the wait times for memory access are even longer
* One increasingly popular way to increase memory bandwidth is to integrate memory controllers into the CPU and attach memory to each CPU
  * One disadvantage to this architecture is that interconnects between processors have to be used when accessing memory attached to another processor
