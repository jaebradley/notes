# [Im going back to writing code by hand](https://blog.k10s.dev/im-going-back-to-writing-code-by-hand/)
* Author owns a vibe-coded project that displays kubernetes pods
* Eventually, couldn’t fix a feature without prompting so had to introspect the project
* Author found a 1,690 line `model.go` file
  * This file contained a model that had UI widgets, clients, caching, etc
* AI builds features, not architecture
  * Author used examples of specific features being special-cased inside a conditional
  * And every new feature had a similar branch
  * AI has a hard time seeing this pattern’s value decay over time because each prompt only touches a single code path
  * Create a design doc that is referenced in a `CLAUDE.md` document with concrete sets of rules around where logic lives, what different components do and interact
* God object is the default AI artifact
  * AI gravitates towards a single-struct-holds-everything because it satisfies the initial prompt with minimal ceremony
* Velocity illusion widens scope
  *  AI made it feel like each feature was “free” even though each feature was another branch in the god object logic
  * Write a vision document that explicitly says who you’re *not* building for and reference this scope boundary in the `CLAUDE.md` file
* AI tends to flatten data because it is the shortest path from “fetch data” to “render table”
  * Author’s example is data that was best modeled as a mapping, but was flattened into an array
  * Any time a new key/value pair was added in-between two values in the mapping, `array[x]` no longer pointed to the correct key/value pair
  * Need to tell the LLM not to flatten structured data, and to keep structured data until rendering that data
  * Build structs that make impossible states impossible - use strong types (vs. `String`s everywhere) so that the LLM won’t accidentally compare types of different values

## [HackerNews discussion](https://news.ycombinator.com/item?id=48090029)
* Mitigations discussed in article are not robust as there will be future features that will clash with added rules
* Commenter’s rules when working with coding agents
  * Must be confident that any generated logic could be implemented by hand, given time
  * If it is not something that the author is confident that they could implement by hand, they do not move on until they completely understand what has been generated and could recreate it themselves, by hand
* Another commenter said that the three months they spent going hard into agentic coding felt wasted and that they failed their users by building useless features, increasing bugs, and losing the mental model of the logic