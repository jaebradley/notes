# [Monoliths are the future](https://changelog.com/posts/monoliths-are-the-future)

* "We're gonna break it up and somehow find the engineering discipline we never had in the first place"
* End up creating many deployables / distributed monolith
* Trading functions and class instantiation for passing messages over network (which they can't always reliably get stuff back from)
  * So now went from writing bad code to writing bad infrastructure (that you deploy bad code on top of)
* Microservices make sense in the context of building a mobile API for a mobile app that's different from the APIs defined by the monolith
  * Mobile API will still connect to the monolith

