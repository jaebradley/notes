# [What is a service mesh?](https://www.nginx.com/blog/what-is-a-service-mesh/)

* Configurable low-latency infrastructure layer designed to handle high volume of network-based communication among application infrastructure services
* Service mesh ensures communication among containerized and ephemeral application infrastructure services is fast, reliable, and secure
* Capabilities include service discovery, load balancing, encryption, observability, traceability, authentication, and authorization, and support for circuit breaker pattern
* Service mesh is implemented using a sidecar / proxy instance for every service instance
  * Sidecars handle interservice communication, monitoring, and security-related concerns
* Container orchestration framework - a separate tool for monitoring and managing a set of containers (especially as the set of containers grows as application grows)
* Services and instances
  * Instance is single running copy of a microservice
    * Can be single container
    * In Kubernetes, instance is made up of small group of interdependent containers (pods)
    * Clients rarely access instance / pod but access a service
    * Service is a set of identical instances or pods which service as replicas when service needs to scale and increases fault-tolerance
  * Sidecar Proxy - runs alongside instance / pod and routes traffic to/from adjacent container
    * Many service meshes use sidebar proxy to intercept and manage all incoming / outgoing traffic
  * Encryption - Service mesh can encrypt / decrypt removing responsibility from each of the services
    * Implemented using mutual TLS which uses private/public keys that are distributed for use by sidecar proxies
  * Authentication and authorization - service mesh is responsible for authorizing and authenticating requests made from both outside and within app
* Data Plane - part of service mesh application that manages network traffic between instances
* Control Plane - Generates and deploys configuration that controls data plane's behavior
