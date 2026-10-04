# [Hexagonal Architecture – What Is It? What Are Its Advantages?](https://www.happycoders.eu/software-craftsmanship/hexagonal-architecture/)

## Disadvantages of the Layered Architecture
* Classic layered architecture has an organization strategy that looks something like `client -> presentation layer -> business logic layer -> data access layer -> database`
* Because all entities, repositories, and ORM libraries are also available at the presentation layer, this can tempt developers to let the boundaries between layers weaken
* The coupling between different layers can also make upgrading databases or a new ORM library version difficult as such an update would require adjustments in all layers of the application
