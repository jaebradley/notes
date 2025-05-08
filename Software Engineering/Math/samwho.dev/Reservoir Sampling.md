# [Reservoir Sampling](https://samwho.dev/reservoir-sampling/)

* How to randomly pick from a set of cards when only shown one card at a time?
  * Don't know how many cards are going to be shown
  * Can only hold on to one card at a time
  * Can't go back to a previous card
* Example: log service that sees burst traffic
* When the log service sees more than the threshold number of logs, it samples some percentage of the burst traffic
* Instead of flipping a coin to decide if we'll hold a card or not, we give each card a `1/n` chance of being kept where `n` is the number of cards we've seen so far
* In order to be fair, every card must have an equal chance of being selected
* For the second card, we want both cards to have a `1/2` chance
* For the third card, we want all cards to have a `1/3` chance

## Choosing multiple cards
* Instead of `1/n` chance, there's now a `k/n` chance where `k` is the number of cards we want to choose
* When deciding to replace a held card, we choose one of the `k` cards we are holding, at random
* Create an array of size `k`
* For each new card, generate a random number between `0` and `n`
* If the random number is less than `k`, replace the card at the randomly-generated index with the new card, otherwise discard the new card

## Applying this to log collection
* Set `k` to `5` so always holding at most `5` log messages
* Every second, send the logs to the log collection service
* Empty array and start again
* Logs are no longer smooth (i.e. they're not a real-time stream of logs) but rather chunks of logs sent at an interval
* The sent logs never exceed the desired threshold
