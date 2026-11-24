# [Agentic Coding: Bet on the Primitives](https://www.robinwieruch.de/agentic-coding-bet-on-the-primitives/)
* Author used LLMs to build custom chart components built on top of D3's math primitives vs. using the Recharts library
* Author's experience inverted a rule the author had previously learned: never hand-roll what a library already solved
* An abstraction is encoded, prepaid implementation labor
  * The cost of this "labor" is flexibility - the library's opinions about rendering, styling, behavior are bundled with its solutions
  * When your project's requirements leave the library's happy path is when friction is incurred in the form of wrapper components, prop escape hatches, etc
* Author views distinction of unopinionated vs. opinionated as "does the dependency compute something for you or does it decide something for you"
  * Scale that maps numbers to pixels is unopinionated about what you draw with them
  * A charting library that owns SVGs, animation lifecycles, tooltip behavior, and exposes configuration props to control these options with is a framework
* If a team does not have an agentic workflow or the maintenance responsibility to maintain a custom primitive library then implementation labor has not gotten cheaper, and a hand-rolled chart is still three weeks of work plus indefinite maintenance
* Every library decision has been a bet on where your project's requirements land relative to the library's happy path
  * The closer your needs sit to that happy path, the more the library makes sense and the further these needs drift towards custom requirements, the more primitives make sense
  * Author contends that agentic coding moves the crossover point dramatically towards the primitives
    