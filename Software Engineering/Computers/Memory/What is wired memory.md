# [What is wired memory?](https://apple.stackexchange.com/questions/31801/what-is-wired-memory)

* Wired memory belongs to the kernel
* Wired memory is used for core functions of operating system - keeping track of open fiels and network connections, page tables

## Library Analogy

* Imagine an enormous library
* No way to fit all the books into a single building
* Most books are kept in a warehouse, but anything that’s been used recently is kept in the library
* If you show up at the library looking for a book, and its in the stacks, you can read it immediately
* If not, you ask a librarion for the book, and the book will be transported from the warehouse and available at some time in the future
* The information in “wired memory” would be the card catalog, keys to the book delivery trucks, maps of the route to the warehouse
* This information can’t be stored in the warehouse as you need this information to retrieve information from the warehouse in the first place
  * This data must be kept in the library at all times
