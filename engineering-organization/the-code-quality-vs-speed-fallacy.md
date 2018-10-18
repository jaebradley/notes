# [The Code Quality vs. Speed Fallacy](https://medium.com/@asciamanna/the-code-quality-vs-speed-fallacy-f4eea147b23e)

* Fallacy is that you can't ship code quickly and care about quality
* Coding as fast as possible is the *only* responsibility for a professional developer
  * Obviously this is a little facetious but the fact remains that self-improvement, mentoring, and improving readability of code are all very important
* A tell tale sign of organizations with quality problems is one where people have no time to learn
  * Developers are always reacting - is something broken in prod, how hard is it to get features in production
* When developers claim they are "too busy shipping" what they really mean is that they don't feel empowered by the organization to slow down and actually think about the problem
* **Their first software design idea is the one that ends up in production**
  * Even once it becomes clear the design is flawed there's pressure to force it to get merged
  * This is because they are rewarded for releasing something on time vs. considering long-term effects the code has on the rest of the organization
* Adds more cruft to the codebase and slows down future work
* People argue that developers who care about quality sit in cubicles all day refactoring code to make it pretty without ever shipping code to production or giving a single thought to org's commitments
  * The developers that care about quality also care about moving quickly - slowing down to ensure that code is always in a state that allows for easy modification is the only way to improve the effectiveness of development teams over time
* The barometer for high quality codebases is that they are as easy to change on day 1 as they are on year 10
* Since code will always be read and maintained, optimizing for readability is key
  * Continuously improving code so that it is easily understood should be a focus of development teams
  * All too often, teams view this type of activity as wasted time and nitpicking
* Tech debt has come to mean any poor quality code to get something done quickly
  * According to originator, Ward Cunningham, incurring tech debt required code to be of high quality so that it was easy to change so that the debt could be reversed in the future
