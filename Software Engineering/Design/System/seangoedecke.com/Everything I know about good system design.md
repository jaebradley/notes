# [Everything I know about good system design](https://www.seangoedecke.com/good-system-design/)
* Good design looks underwhelming
* Thoughts like "this ended up being easier than I expected" or "I never have to think about this part of the system, it's fine"
* Bad design is more impressive than good
  * Be suspicious of impressive-looking systems
* Beginning from scratch with a complex system is not a good idea
* The hard part about software design is state
* If you're not storing information, your app is stateless
  * Example is GitHub API that takes a PDF and returns an HTML rendering of the PDF file
* Minimize stateful components as stateful components can get into a bad state
* In practice, means that one service knows about state and there are other services that do stateless things
* Avoid five different services that write to the same table and isolate the writing logic in one service
* When querying the database, it's almost always more efficient to get the database to do the work than to do it yourself
  * JOIN the data together instead of making separate queries and stitching them together in-memory
* Use a document store like S3 as a large-scale persistent cache
  * Store the result of a really expensive operation in S3 and serve it from there
* Push-based systems usually means putting each push on an event queue and having many event processors pulling from the queue and sending out the pushed events
* Pull-based systems usually means standing many read-replica cache servers that sit in front of the main application and handle read traffic
* Hot paths are important because they have fewer possible solutions than non-hot paths
* Log aggressively during non-happy paths
* One or two very slow requests are scary because they are disproportionately from your largest and most important users
* Idempotency key is a UUID sent in a request that another service uses to avoid re-running old requests
* If rate-limiting code uses a Redis bucket to check if a user has made too many requests and the Redis bucket is unavailable what should the logic do? Fail openly and let the request through, or fail "closed" and block the request with a 429?
