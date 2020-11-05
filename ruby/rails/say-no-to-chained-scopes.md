# [Say No To Chained Scopes!](http://craftingruby.com/posts/2015/06/24/say-no-to_chained-scopes.html)

* Having controllers that do direct querying with very specific `where` statements leak database implementation details into the controller layer
* One strategy is to move the `where` statements to their own `scope`s and then chain the scopes like `Person.male.adult.left_handed`
* Downside is that it makes testing difficult as there are now mocks for each scope method that need to be created and connected
  * Also, most of the tests depend on the ordering of scopes, the tests will fail (since the mocks are all connected / expected in a certain order) so the test is brittle
  * For more complex scopes, they may not be able to just be combined with other scopes to generate value SQL
* So either combine the scope in a single scope or class method (so in this case, a class method called `left_handed_male_adults`)
  * Makes testing easier as a single mock / expectation is needed to test that portion of the controller's logic
