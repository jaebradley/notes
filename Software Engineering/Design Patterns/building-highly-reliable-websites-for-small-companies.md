# [Building Highly Reliable Websites For Small Companies](https://www.kalzumeus.com/2010/04/20/building-highly-reliable-websites-for-small-companies/)

* Software systems are also, overwhelmingly, killed by their moving parts
* Decouple where possible (i.e. make it so that a failure of a particular component can't bring down an entire system)
  * Never call an external API from within an HTTP request/response cycle
  * Makes system 100% dependent on external API being constantly available
  * Do all communication with external APIs asynchronously
    * Queue up a delayed job to do the API call async
    * If user needs to see result of API call, poll server asking if job is completed yet and if it hasn't completed in a reasonable amount of time, send error message to user
* Minimizing operator error is critically important because you are the least reliable component of your system
* If you have to do it more than once, it should be automated or made into a checklist
* Take 15 minutes out of your busy schedule every quarter and actually run the checklist to make sure it still works
* Checklists that are procedures that can't fail or require no judgement shouldn't be checklists but shell scripts (should be well-tested and in version control)
* Create internal status page which automatically checks crucial / risky pieces of information like if an external API provider goes down