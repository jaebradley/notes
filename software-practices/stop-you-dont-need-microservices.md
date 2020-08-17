# [Stop!! You Don't Need Microservices](https://medium.com/swlh/stop-you-dont-need-microservices-dc732d70b3e0)

* Microservices have a higher baseline cost than monolithic services due to their inherent complexity and maintenance costs
* Do individual components of your application need to be scaled? (Which is one of the main advantages of a microservices-based architecture)
* Microservices means implementing transactions across services 
  * Microservices introduce eventual consistency issues because of their laudable insistence on decentralized data management. With a monolith, you can update a bunch of things together in a single transaction. Microservices require multiple resources to update, and distributed transactions are frowned on (for good reason). So now, developers need to be aware of consistency issues, and figure out how to detect when things are out of sync before doing anything the code will regret
* If services need to communicate frequently, microservices now introduces network latency between services
  * Where there was once basically no overhead between services communicating in a monolith, this changes the latency unit from nanoseconds to milliseconds
* Testing becomes more difficult to orchestrate whereas a monolithic application allows execution of tests nearly immediately
