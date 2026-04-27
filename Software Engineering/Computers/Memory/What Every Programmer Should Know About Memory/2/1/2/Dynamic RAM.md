# Dynamic RAM

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
* The structure of the DRAM cell is also simpler and more regular which means packing many of them close together on a die is simpler
* The cost difference wins - most hardware has main memory based on DRAM

