# [What Every Programmer Should Know About Memory](https://www.gwern.net/docs/cs/2007-drepper.pdf)

## Commondity Hardware Today

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

## RAM Types

### Static RAM (SRAM)

* One SRAM cell requires 6 transistors
* Maintaining the state of the cell requires constant power
* The cell state is available for reading almost immediately once the word access line is raised
* The cell state is stable, and no refresh cycles are needed

### Dynamic RAM (DRAM)

* Consists of one transistor and one capacitor
* The DRAM cell keeps its state in the capacitor
* The transistor is used to guard access to the state
* To read the state of the cell, the access line is raised, which causes a current to flow on the data line DL or not, depending on the charge in the capacitor
* To write to the cell, the data line is appropriately set and then the access line is raised for long enough to charge or drain the capacitor
* The use of a capacitor means that reading the cell discharges the capacitor
* The capacitor must be recharged at some point, and it must be recharged relatively often (once every 64 ms)
  * During a refresh cycle, no access tot he memory is possible since a refresh simply reads memory where the result is discarded
* Reading a cell causes the charge of the capacitor to be depleted
  * This means that every read operation must be followed by an operation to recharge the capacitor
  * This means that reading memory content requires additional energy and time
* Charging and raining a capacitor is not instantaneous
* The chip real estate needed for one DRAM cell is many times smaller than that of an SRAM cell
* The SRAM cells also need individual power for the transistors maintaining the state
* The structure of the DRAM cell is also simpler and more regular which means packing many of them close toegether on a die is simpler
