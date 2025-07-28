# [All AI Models Might Be The Same](https://blog.jxmo.io/p/there-is-only-one-model)
* Author's example of "Mussolini or bread?"
  * Game similar to 20 questions, except this game simply refines the match based on asking the same question ("is the concept closer to Mussolini or bread?") over and over again
* Idea is that human brains build up complicated models of the world
  * However, these models are pretty similar between individuals
  * Proof is that we can narrow down almost any concept by repeated asking the same question
* One perspective of AI is that we're learning to compress all the data in the world
* Bigger language models give better probability distributions
* Smarter language models are better data compressors
* When a model can fit the training data perfectly, it has effectively memorized the data really fail, and fails to generalize
  * When the dataset gets too big,the model can no longer fit the data
  * Model is forced to combine information from multiple datapoints - this is generalization
* Author's contention is that generalization occurs in the same way, even within different models
  * Corollary is that given certain parameters, there is only one way to compress data _well_
* Author worked on a problem where given a representation vector from a neural network, can the text inputted into the network to generate that representation vector be inferred?
* Were unable to use this inversion model for different learning models
  * This was unexpected as (theoretically) different models should be learning the same thing, so a _unversal_ inverter should exist
* Problem difficulty was due to a lack of correspondence - having text in space A and B that can be aligned
  * CycleGAN proposed a way to translate between spaces that lacked correspondence using a method called cycle consistency
