# Vertical Slice Architecture

## [I like this folder structure. Why am I wrong?](https://www.reddit.com/r/ExperiencedDevs/comments/1lz537p/i_like_this_folder_structure_why_am_i_wrong/)
* Slice represents a use-case (single responsibility principle)
* CRUD naturally works well with VSA, but other workflows like background processing is not as obvious
* SRP = "module/class should only have one responsibility" but maybe it's better understood as "class or module should only serve one actor"
* If you have a class that is servicing two separate actors, when one actor requests a change to that class, there is a potential for the change to impact the other actor - SRP seeks to mitigate this risk
* Service class is tightly coupling the domain to the API interface. Hard to reuse the service class for other use-cases

## [Software Architecture – Part 4: Vertical-Slice Architecture](https://lionadi.wordpress.com/2022/07/23/software-architecture-part-4-vertical-slice-architecture/)
* Vertical Slices are about focusing on features
* Take all technical concerns related to a feature and organize that code together
* Slices are for the most-part, self-contained - they don't couple with other slices
* Keep things together that change together
* Each slice can have its own method of data access into a data store
* May end up with code duplication - price of self-containment and loose coupling
* Related to CQRS: Commands are intents to take actions, like changing state. Queries return data, and do not create side effects
  * Slicing the business domain "vertically" by operations that take place within this business domain
  * Helps focus on specific business operations
