# [Nobody Gets Promoted for Simplicity](https://terriblesoftware.org/2026/03/03/nobody-gets-promoted-for-simplicity/)
* Nobody gets promoted for complexity they avoided
* Need to evaluate if creating abstractions to avoid duplication ends up with software that is easier to maintain and understand
* When you look at "simple" software engineering (both implementation and architecture), there isn't magic, cleverness

> Anyone can add complexity. It takes experience and confidence to leave it out.

* As an engineer, describe unnecessary architecture that was avoided

> But *“evaluated three approaches including an event-driven architecture and a custom abstraction layer, determined that a straightforward implementation met all current and projected requirements, and shipped in two days with zero incidents over six months”*, that’s the same simple work, just described in a way that captures the judgment behind it. The decision *not* to build something is a decision, an important one! Document it accordingly.

* In design reviews ask *“what’s the simplest version we could ship, and what specific signals would tell us we need something more complex?”*
  * Frame simplicity as the default and put the burden of proof on complexity, and not the other way around
* During promotion discussions, if a candidate's packet is a buzzword salad, ask if all of the impressive-sounding technology was necessary

## [Hacker News Thread](https://news.ycombinator.com/item?id=47246110)
> AI coding tools are making this problem worse in a subtle way. When an agent can generate a "scalable event-driven architecture" in 5 minutes, the build cost of complexity drops to near zero. But the maintenance cost doesn't.

> The real test for simplicity has always been: can the next person who touches this code understand it without asking you?
