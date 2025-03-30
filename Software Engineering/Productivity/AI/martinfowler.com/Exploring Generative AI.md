# [Exploring Generative AI](https://martinfowler.com/articles/exploring-gen-ai.html)

* Author categories 3 types of AI “misses”
  * Slowed down speed of development, time to commit instead of speeding it up
  * Friction for the team’s flow after commit
  * Negatively impacts long-term maintainability of the code

## Error Type: Time to commit
* Least problematic impact radius / most obvious failure mode

### Misdiagnosis
* AI goes down rabbit holes where it misdiagnoses a problem
* Use previous experience with the problem to assess misdiagnosis
* Author’s example: AI assumed Docker build issue was due to architecutre settings for that Docker build and changed settings based on those assumptions.
  * In reality, the issue was due to copying `node_modules` built for the wrong architecture

## Error Type: Team flow in iteration

### Too much up-front work
* Instead of incrementally implementing working slices of functionality it will make a broad scale change / change that functionality everywhere
* Author’s example: frontend tech stack migration, AI converted all UI components at once vs. starting with a single component and logic tied to that component that would integrate with the backend

### Brute-force fixes instead of root cause analysis
* Author’s example: when encountering a memory error during a Docker build, it increased the memory settings rather than questioning why so much memory was used in the first place

### Complicating the developer workflow
* AI generated build workflows created a poor developer experience
* Author’s examples are mostly around introducing complicated build setups that confused author and AI

### Misunderstood or incomplete requirements
* When the prompt description and functional requirements are not detailed enough, the AI will jump to incorrect conclusions.
* The AI needs manual intervention and attention
* Depending on when this is caught, will require back-and-forth to correct the work

## Error Type: Long-term maintainability
* Most insidious because the feedback loop can be weeks / months later
* Cases where the code will work fine for now, but will be harder to change in the future
* This category is where author’s 20+ years of experience mattered the most

### Verbose and redundant tests
* AI frequently created new tests vs. adding assertions to existing ones or added assertions that already existed
* The more tests and assertions that are duplicated, the harder they are to maintain

### Lack of reuse
* AI-generated code lacks modularity, making it difficult to apply the same approach elsewhere
* Author’s examples: Not realizing a UI component is already implemented elsewhere and can be reused vs. duplicated.
  * Another example was using inline CSS styles instead of CSS classes and variables.

### Overly complex or verbose code
* Requires manual intervention to remove unnecessary elements from AI-generated code
* Includes code that is more complex that will lead to problems when extending the code in the future
* Author’s examples: AI generates CSS, author has to remove massive amounts of redundant CSS styles
  * Another example: AI generated web component that built elaborate version of a feature that was not needed
  * Another example: When refactoring, AI failed to recognize existing dependency injection chain, introducing unnecessary additional parameters that were already injected as part of the current constructor call

## What can you do to safeguard against AI missteps?
* Carefully review AI-generated code
* Be cautious of “good enough” solutions that were miracalously created in a short amount of time, but introdcue long-term maintenance costs
