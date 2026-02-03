# [How I estimate work as a staff software engineer](https://www.seangoedecke.com/how-i-estimate-work/)
* Most programming in large systems is research: identifying prior art, mapping out enough of the system to understand the effects of changes, etc
* Author thinks that those that expect these research questions to be answered during the planning process are a throwback to the days where a single architect would map out all the work in advance, and individual implementers had to mechanically follow instructions
* If an engineering team comes up with a large estimate for a project that a VP really wants, they will be pressured into lowering their initial estimate
* Estimates are political tools for non-engineers in the organization - they help managers, VPs, directors, senior leadership decide which proejcts get funded and which projects get cancelled
* What actually happens with estimates is that teams start with an estimate and try and figure out what kind of software work they can do to meet the estimate

## How the author estimates work
* Author tries to understand the political context of the project
  * Casual ask? Incredibly important project to the business?
  * What kind of estimate is management looking for? "CTO wants this in 1 week" vs. "this work might fit the core competencies ofyour team"
* Write up a risk assessment, and not a concrete estimate
  * Don't say "this is an X week project"
  * Instead say something like "I don't think this project will get done in Y weeks, because A, B, and C would all need to go according to plan, and at least one of those things is bound to take more work than expeted"
  * Ideally, there is a series of plan
    * "Tackle A, B, and C directly but could take Y weeks"
    * "Do A, but bypass B, and C, which introduce risks D, and E, but will allow completion of work by deadline"
    * "Team Q is more familiar with A and B, and they can implement changes to A and B, and we can focus on C"
  * Job is to figure out the set of software approaches that match the estimate as the management chain already knows how long they want it to take
