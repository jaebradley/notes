# [The Reversal Curse: LLMs trained on “A is B” fail to learn “B is A”](https://arxiv.org/pdf/2309.12288v2.pdf)

* If a large language model is trained on a sentence of the form "A is B" it will not automatically generalize the reverse information "B is A"
  * "If X was the ninth Chancellor of Germany" it will not be automatically able to answer the question "Who was the ninth Chancellor of Germany?"
* GPT-4 is able to answer questions that are in the same order as the training data ~80% of the time, compared to ~35% of the time for reversed questions 
  * "Who is Tom Cruises's mother?" vs. "Who is Mary Lee Pferiffer's son?"

## Meta-learning

* LLMs are trained to predict what humans would write and not what is true
* Sentences of form "<name> is <description>" and "<description> is <name>" often both exist in pretaining datasets
  * If the former appears, the latter is more likely to appear
  * Humans vary the order of elements in a sentence or paragraph
* A good meta-learner would increase the probability of an instance of "<description> is <name>" after being trained on "<name> is <description>"
