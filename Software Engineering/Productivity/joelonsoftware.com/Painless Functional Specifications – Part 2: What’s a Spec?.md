# [Painless Functional Specifications – Part 2: What’s a Spec?](https://www.joelonsoftware.com/2000/10/03/painless-functional-specifications-part-2-whats-a-spec/)
* Functional specification: how a product will work from the user's perspective
* Technical specfication: describes internal implementation of the program like data structures, relational database models, algorithms, etc
* Functional specs have a single author (and not a team)
  * For a big product, split the spec up into areas and give each area to a different person
  * > Other companies think that it’s egotistic or not “good teamwork” for a person to “take credit” for a spec by putting their name on it. Nonsense. People should take responsibility and ownership of the things that they specify. If something’s wrong with the spec, there should be a designated spec owner, with their name printed right there on the spec, who is responsible for fixing it. 
* Nongoals are features that will not be built, or focus areas that will be ignored ("we won't care about performance in this release, and will optimize is subsequent releases")
* When you're designing a web-type service, a good way to do this is to give every possible screen a cnanonical name, and provide a chapter describing each one in great detail
* Details like all error cases (invalid email address, incorrect password, etc)
  * Cases correspond to decisions that somebody (product manager?) is going to have to make - the product manager needs to decide what the policy is going to be for a forgotten password
* By the time implementation starts, any open issues in the speci need to be identified
  * You will have enough problems resolving new issues that occur when programmers implement code without having old open issues
* Specs should be updated frequently
  * Infrequently updated specs that don't reflect the latest product decisions are why specs get a bad reputation
