# [SmartStack: Service Discovery in the Cloud](https://medium.com/airbnb-engineering/smartstack-service-discovery-in-the-cloud-4b8a080de619)

## The SmartStack Way

### Nerve

* Creates ephemeral nodes in Zookeeper, which contains information about the address/port combinations for a backend available to serve requests for a particular service
* In order to know whether a particular backend can be registered, Nerve performs health checks
  * Every service that you want to register has a list of health checks, and if any of them fail, the backend is de-registered
* Health check can be as simple as "is this service reachable over TCP / HTTP
  * At Airbnb, every HTTP application exposes a `/health` endpoint
* For non-HTTP services, health checks execute some basic functionality like `redis` might try a `set` and `get` 
* To debug or execute maintenance on a machine, stop the Nerve process on the machine


### Synapse

* Runs beside your service, and reads information from Zookeeper for available backend services, and then configures an HAProxy process
* When a service wants to talk to another service, it talks to the local HAProxy, which routes the request
* Synapse watches Zookeeper to reconfigure HAProxy when new services are deployed / taken off-line
