# [What Are Abstractions in Software Engineering with Examples](https://thevaluable.dev/abstraction-type-software-example/)

## What’s an Abstraction, Exactly?
* Concepts associated with "abstraction": "hiding" or "removing", "generalization", "idea vs. reality"
* "Abstraction is the purposeful suppression, or hiding, of some details of a process or artifact, in order to bring out more clearly other aspects, details, or structure"

## Wonderful Washing Machines
* Author uses a washing machine as an example of abstraction
* The _interface_ for the washing machine are the buttons/knobs that set a different washing "program" given the intended washing behavior (delicate clothes vs. sheets)
* To actually wash the clothes, it is not necessary to know how the washing machine works internally
  * So, it is not necessary to know about the drums, water valves, thermostat, etc. and how they work together in order to wash these clothes
 
## Control Abstraction
* Function names should simplify and hide internal details
* The function name + the function inputs + the function outputs (i.e. the function signature) whould provide all the necessary details
* A function generalizes behavior: this behavior can be reused anywhere

## Abstraction and Indirection
* Author makes argument that language-based `interface`s (i.e. "interface constructs") is more about indirection than abstraction
* Implementing a concrete `YamlParser` class that implements a `Parser` "interface construct" does not necessarily hide details, since all `Parser` methods need to be implemented
  * `YamlParser` is also not generalized, for the same reason (specific implementation)
* Instead, using the "indirect" "interface construct" adds flexibility - the `YamlParser` can be swapped with a `JsonParser` if they both use implement the `Parser` interface
* Abstractions may need to be repaired, so as developers, we must have some knowledge about what is behind the abstraction, i.e. the underlying complexity that the abstraction is trying to hide, in order to understand how to fix the abstraction when it "leaks"
