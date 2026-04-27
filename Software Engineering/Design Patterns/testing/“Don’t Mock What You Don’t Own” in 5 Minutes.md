# [“Don’t Mock What You Don’t Own” in 5 Minutes](https://hynek.me/articles/what-to-mock-in-5-mins/)

* Don't mock what you don't own means that whenever you employ mock objects, you should use them to substitute your own objects and not third-party ones
* Example of a business logic method that makes two HTTP requests and stitches the request data together
  * To test such a method, need to mock the underlying HTTP client, mock the `get` method on the client, mock the response, and mock the response's `json` method to return the data you expect
* The purpose of business logic tests should be obvious when initially reading them

## Polite Mocking

* Add a `DockerRegistryClient` class that depends on the underlying HTTP client and exposes two methods that each execute the two different HTTP get requests from before
* Now, the previous business logic that had to know about the HTTP client and the method / resources to call, only calls the `DockerRegistryClient`'s relevant methods
* The test is much simpler since the test just relies on the `DockerRegistryClient` instead of having to mock many levels of the HTTP client (and its response(s))
* If a different HTTP library was chosen in the future, the `DockerRegistryClient` interface should not change, so the business logic should not change either
