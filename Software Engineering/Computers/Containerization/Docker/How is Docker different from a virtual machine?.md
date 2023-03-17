# [How is Docker different from a virtual machine?](https://stackoverflow.com/questions/16047306/how-is-docker-different-from-a-virtual-machine)

## [Answer 1](https://stackoverflow.com/a/34757096/5225575)

### How virtualization works
* The virtual machine manager takes over the CPU ring 0, or the root mode in newer CPUs) and intercepts all privileged calls made by the guest OS to create the illusion that the guest OS has its own hardware
* The folks at VMware were the first who had an idea to rewrite the executable bytes in memory for privileged calls of the guest OS to achieve this
* The net effect is that virtualization allows you to run two completely different OSes on the same hardware
* Each guest OS goes through all the processes of bootstrapping, loading the kernel, etc
* A guest OS can't get full access to the host OS or other guests

### How containers work
* Around 2006, a new kernel level feature was implemented called namespaces
* One function of the operating system is to allow sharing of global resources like network and disks among processes
* What if these global resources were wrapped in namespaces so that they are visible only to those processes that run in the same namespace?
  * So you can get a chunk of disk and put that in namespace X and then processes running in namespace Y can't see or access it
  * Processes in namespace X can't access anything in memory that is allocated in namespace Y
  * Processes in namespace X can't see or talk to processes in namespace Y
  * Provides a kind of virtualization and isolation for global resources
* Each container runs in its own namespace but uses exactly the same kernel as all other containers
  * The isolation happens because the kernel knows the namespace that was assigned to the process and during system calls to make sure that the process can only access resources in its own namespace

### Limitations

* Can't run completely different OSes in containers like in VMs
* Can run different distros of Linux because they do share the same kernel
* When you load a new container, an entire new copy of the operating system doesn't start like in a VM (again, all containers share the same kernel)
* Don't have to pre-allocate a significant chunk of memory to containers because they are not running anew copy of the OS like VMs
