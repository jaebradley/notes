# [Some Virtual Threads Internals](https://blog.rockthejvm.com/ultimate-guide-to-java-virtual-threads/#8-some-virtual-threads-internals)

* Virtual thread stores information of what must be run
* Instantiating a `VirtualThread` basically generates a virtual thread in the `NEW` status
* A scheduler is chosen (if unspecified, the default scheduler is chosen)
* Once the `start` method is called, the virtual thread moves to the `STARTED` status
* When the associated `Runnable` / "continuation" is executed, the virtual thread is moved to the `RUNNING` status
* Every time the virtual thread reaches a blocking point, the state of the thread is changed to `PARKING`
* When in `PARKING`, the virtual thread is attempted to be unmounted from the carrier thread
