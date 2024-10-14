# [How LLMs Work, Explained Without Math](https://blog.miguelgrinberg.com/post/how-llms-work-explained-without-math)

## Tokens

* Token is the basic unit of text understood by an LLM
* The complete list of tokens used by an LLM is said to be the LLM's vocabulary
* 50k vocabulary of the GPT-2 language model
* Each token in an LLM's vocabulary is given a unique identifier
* LLM uses a tokenizer to convert between regular text and an equivalent sequence of tokens
* Byte-pair encoding algorithm doesn't always map entire words to tokens
  * Infrequent words do not get their own tokens and have to be encoded with multiple tokens
  * Example: `Payment` = token `19197` (`Pay`) and token `434` (`ment`)

## Next Token Predictions

* A language model makes predictions about what token will follow an input set of tokens (the "prompt")
* Training involves presenting the language model with lots of text
* Model is able to calculate next token probabilities for a given token sequence given the text it saw during training

## Generating Long Text Sequences

* Model only predicts the next token
* To generate complete sentences, run the model multiple times in a loop
* Each loop iteration generates a new token
* Token is added to the input tokens and then the loop is run again, until sufficient text has been generated

## Model Training

* Simple way to train a model is to create a table of all pairs of consecutive tokens that appear in a training dataset and build a table of probabilities with them
* So given the sentence "I like apples", the cell at row value "I" and column value "like" would have a count of `1`
  * Row value "like" and column value "apples" would also have a count of `1`
  * Continue to do this same pairing for many sentences, and the table represents the expected next token frequency given a token
* Due to this frequency / probability-related selection of the next token, new sentences can be generated where the sentence was not part of the training data set
  * This is how these models come up with original ideas / concepts, by reusing patterns and stitching together different bits of what was learned in training

## Context Window

* Issue with (simple) example prediction technique is that only the previous token influences the next token
  * In other words, the _context window_ of this solution is equal to one token
* For longer context windows, the table approach from before is extended for each pair of tokens, triplicate of tokens, etc. (for whatever context window length is desired)
* This Markov Chain approach has large scalability problems at even small numbers of tokens and window sizes
* GPT-2 has a 1024 token context window, GPT-3 has a 2048 token context window, and GPT-4 has a context window to 128k tokens

## From Markov Chains to Neural Networks

* The training process for neural networks consists of finding the parameters that make the function perform best when evaluated on the data from the training dataset
* During the training process, the parameters are iteratively adjusted in small increments using an algorithm called backpropagation
* GPT-4 is said to have about 1.76 trillion of these parameters

## Layers, Transformers, and Attention

* A neural network is configured to perform a chain of operations - a layer
  * Each layer "transforms" the input before passing the data onto the next layer
* Distinctive characteristic of transformer models is a layer calculation called "Attention"
  * Allows them to derive relationships and patterns between tokens that are in the context window, modifying the probabilities for the next token
  * Initially was used in language translators, to find which tokens in an input sequence were most important to extract meaning
