# [The Wrong Abstraction](https://sandimetz.com/blog/2016/1/20/the-wrong-abstraction)

## Principle

* Duplication is far cheaper than the wrong abstraction
* Prefer duplication over the wrong abstraction

## What ends up happening

* Existing duplication is abstracted
* New requirement appears, existing abstraction is almost perfect
* Implementer feels like they need to retain the existing abstraction
* Alter code to take parameter and add conditional logic
* So now universal abstraction behaves differently for different cases
* Another use-case, another parameter, another conditional

## Approach in these cases

* The more complicated / incomprehensible the code, the greater the sunk cost fallacy to retain it
* When dealing with the wrong abstraction, the fastest way forward is back
* Re-introduce duplication by inlining the abstracted code back into every caller
* For each caller, determine the subset of inlined code that this specific caller executes
* Delete the logical pieces that aren’t needed for that specific caller
* While this removes both the de-duplication/abstraction and the conditionals, it’s common to find that although each caller was invoking a shared abstraction, the code they were running was actually more unique than expected
* By unraveling the old abstraction, can start re-isolating duplication and re-extracting abstractions
* Continuing to move ahead with the old implementation makes adding new features hard, especially as each new “success” increasingly complicates the code
* If you find yourself passing parameters and adding conditional paths through shared code, the abstraction is incorrect
* Once an abstraction is proved incorrect, the best strategy is to re-introduce duplication and let it show you what’s right
