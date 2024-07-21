# Chapter 3. Managing Domain Complexity

## Inconsistent Models

* Case of a "sales lead" and a "marketing lead"
* Traditional solution is to design a single model that is used for each problem
* Leads to large entity relationship diagrams
* Are supposed to be suitable for everything but become effective for nothing
* Issue with creating a "Marketing Lead" model and a "Sales Lead" model is 
  * Leads to cognitive load - when should one model be used vs. the other?
    * Especially true when the models have implemented in similar ways
  * The model name isn't aligned with the "ubiquitous language" used in each model's context
    * In other words, nobody in a marketing department / context would say "marketing lead" - it would just be "lead". People don't need this extra prefixed information - they can rely on the conversation's context

## What Is a Bounded Context?

* DDD solution = divide the ubiquitous language into multiple smaller languages, then assign each one to the explicit context in which it can be applied
  * This context is its bounded context
* In previous lead example, there are two bounded contexts - marketing and sales
* As long as "lead" bears a single meaning in each bounded context, each fine-grained ubiquitous language is consistent and follows the domain experts' mental models

## Ubuiquitous Language Refined

* A ubiquitous language is not universal
* A ubiquitous language is focused on describing only the model that is encompassed by the bounded context

## Boundaries

### Physical Boundaries

* Bounded contexts serve not only as model boundaries but also as physical boundaries of the systems implementing them
* Each bounded context should be implemented as an individual service/project
* This means it should be implemented, evolved, and versioned independently of other bounded contexts

## Bounded Contexts in Real Life

### Semantic Domains

* Example: `monitor`, `port`, `processor` have different meanings in the software vs. hardware engineering semantic domains
* Another example is the tomato
  * According to the semantic domain of a botanist, a tomato is a fruit (whereas, botancially, a vegetable is a general term encompassing all other edible parts of a plant)
  * According to the semantic domain of a chef, a tomato is a vegetable
  * In the bounded context of US taxation (in 1893, the US Supreme Court classified the tomato as a vegetable) the tomato is a vegetable

### Buying a Refrigerator

* Cardboard "model" of a refrigerator that is just flat piece of cardboard with the floor dimensions of the author's desired refrigerator
* Models solve a problem - the cardboard model in question solves whether or not the refrigerator can fit through a door
* Could build a 3D model of the refrigerator but it wouldn't necessarily solve the problem any more efficiently than the 2D flat cardboard model
  * Building a 3D model is overengineering
* Another model is a tape measure to check the refrigerator's height
* Each model (the 2D flat cardboard model for the base area and the tape measure for the height) omit any extraneous information irrelevant to the problem that each model is trying to solve
