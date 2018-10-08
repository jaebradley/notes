# [`Essential Ruby On Rails Patterns - Service Objects`](https://medium.com/selleo/essential-rubyonrails-patterns-part-1-service-objects-1af9f9573ca1)

* Often times, service objects are named like `UserCreator` or `TwitterAuthenticator`
* Author finds naming service objects after commands or actions more comprehensible - `CreateUser` or `AuthenticateUsingTwitter`
* Pick one way of calling service objects - it could be a `call` method, but `perform`, `run`, or `execute` are valid
  * The class name should always state what the class responsibility is so there's less need for the class method to provide additional context
* One responsibility per service object
  * Instead of creating a `ManageUser` service class which is responsible for creating and deleting users, create `CreateUser` and `DeleteUser` service objects
