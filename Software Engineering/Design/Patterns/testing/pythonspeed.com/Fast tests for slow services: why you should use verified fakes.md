# [Fast tests for slow services: why you should use verified fakes](https://pythonspeed.com/articles/verified-fakes/)
* Example is depending on the Twitter API
  * Actually calling the Twitter API would slow down tests and make them flaky
  * Using a fake or mock test double loses confidence that the logic under test would work in the real world
* A verified fake validates a contract or interface that is expected between `TwitterClient` and `FakeTwitterClient`
* `TwitterContractTests` run against both the `TwitterClient` and `FakeTwitterClient`
* Example: if a message is tweeted, it will show up in the list of tweets
  * This can be encoded in the contract tests, where the fake client generates a random tweet and then that random tweet is returned by the `list_tweets` client method
* Execute the contract tests against the real client and the fake client
  * The real client contract tests should execute in CI once a night or when the relevant code changes
* There are exceptions like network-related exceptions that are difficult to trigger reliably in contract verification tests
  * And if these edge cases cannot be triggered, then there is not a verified fake for the edge case
