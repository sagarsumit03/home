
# Design Patterns:

  

https://medium.com/hackernoon/scaling-microservices-with-message-queues-spring-boot-and-kubernetes-9ba4b0e48bdf

  

https://badia-kharroubi.gitbooks.io/microservices-architecture

  

## Decomposition Pattern:

  

* ### **Decomposition By Business Capability:**
----

* ### **Decomposition By Subdomain:**
---

* ### **Strangler Pattern:**

		Transform, Coexist, and Eliminate

	while converting from Monolith to Microservice, instead of taking out all the components at once, we replace a particular functionality with a new service. They co-exist for a while and once the new service is tested in Parellel, the old functionality is deleted from the Monolith.
	
---

* ### **BulkHead Pattern:**

	Bulkhead pattern is to avoid faults in one part of a system to take the entire system down.
	##### Used By:
	>The bulkhead implementation in Hystrix limits the number of concurrent calls to a component. This way, the number of resources (typically threads) that is waiting for a reply from the component is limited.

	##### Example:

	>Assume you have a request based, multi threaded application (for example a typical web application) that uses three different components, A, B, and C. If requests to component C starts to hang, eventually all request handling threads will hang on waiting for an answer from C. This would make the application entirely non-responsive.

	##### Implementation:

	>Hystrix' implementation of the bulkhead pattern limits the number of concurrent calls to a component and would have saved the application in this case. Assume we have 30 request handling threads and there is a limit of 10 concurrent calls to C. Then at most 10 request handling threads can hang when calling C, the other 20 threads can still handle requests and use components A and B.

  
---
## Cross-cutting Concern Pattern:

  

* **External Configuration:**

	In some cases, it is possible to edit these files to change the behavior of the application after it has been deployed. However, in many cases, changes to the configuration require the application to be redeployed, resulting in unacceptable downtime and additional administrative overhead.

	Local configuration files also limit the configuration to a single application, whereas, in some scenarios, it would be useful to share configuration settings across multiple application
---
* **Service Discovery Pattern:**

	https://hackernoon.com/understand-service-discovery-in-microservice-c323e78f47fd

	https://medium.com/@jamesemyn/service-discovery-in-microsrvice-cbd54afb94f3

	>lets Say Service A needs to do REST API call to service B, in order to make a request we need to	the network location of Service B instance, but when things go cloud it's difficult to know the dynamically changing service B network locations.

	There are 2 Types of Network Discovery:
		 
		1. Client-Side Discovery:

	> the client is responsible for determining the network locations of the available service B instances and load Balancing across them. The Client Queries a Service Registry, which is a DB of available instances. Then uses a load-balancing Algorithm to select one of the instances.

	Example: Eureka and Ribbon for registry and Load Balancing

	##### Pros and Cons:

	Since the client knows about the available services instances, it can make intelligent, application‑specific load‑balancing decisions such as using hashing consistently. One significant drawback of this pattern is that it couples the client with the service registry. You must implement client‑side service discovery logic for each programming language and framework used by your service clients.

  

		2. Server-Side Discovery:

	>The client makes a request to a service via a load balancer. The load balancer queries the service registry and routes each request to an available service instance.

	##### Example: ```Ngnix```

	The proxy plays the role of a server‑side discovery load balancer. In order to make a request to a service, a client routes the request via the proxy using the host’s IP address and the service’s assigned port. The proxy then transparently forwards the request to an available service instance running somewhere in the cluster.

	##### Pros and Cons:

	a. One great benefit of this pattern is that details of discovery are abstracted away from the client.
	b. Clients simply make requests to the load balancer. This eliminates the need to implement discovery logic for each programming language and framework used by your service clients.
	c. Cons: now you need to manage another moving part — the load balancer.
---
* ### **Circuit Breaker Pattern:**

	>Hysterix is used to stop cascading failures, I'll give you an example to explain what I mean: Lets pretend you have 3 components: 1) Frontend, 2) Backend A and 3) Backend B.

	Frontend talks to Backend A and Backend A asks backend B to do some sort of lookup. The Frontend receives 50k requests per second, which means 50k requests are going to Backend A and another 50k requests going to Backend B. If Backend B Becomes unhealthy, that is 50k sockets you're holding open between Backend B to Backend A, and another 50k sockets open between Backend A and the Frontend. What will end up happening is all the servers involved in the transaction will all start to hang because all the sockets are being held open. The sockets will fill up really fast, at 50k a second, with a 20 second timeout, thats 1 million open sockets between each server! the result of Backend B timing out will mean requests to Backend A will timeout which will mean requests to the Frontend will also time out. Hysterix (or the idea of circuit breaking) is pretty much introducing a switch where when a server becomes unhealthy, it will have some sort of way to deal with the errors such as stopping all future requests and just give a predefined response instantly, resulting in the sockets closing straight away and no cascading failures occuring. This results in increased resilience and better fault tolerance.

  

	Now the similar thing Can be achieved by simple try catch block but the key here is, that Hystrix Can use bulkhead and restrict the future calls to be made, till the point the threads are free.

  

	If the reason for the failure of that System B system is some temp network outage / Database slowness, probably its the best to give this service some time and not to try to call it over and over again, we should give it a chance to 'recuperate'. When we apply a circuit breaker, during the "open-circuit" period our code won't even try to call the server, directly routing to the fallback method.

  

A regular try/catch block, on the other hand, will always call the recommendation service.

---
* ### **Blue Green Deployment Pattern:**

		CICD approach.

  
---
## Observability pattern:

  

* ### **Log Aggregation:**

	>Use a centralized logging service that aggregates logs from each service instance. The users can search and analyze the logs. They can configure alerts that are triggered when certain messages appear in the logs.

	Suppose you have an e-commerce system powered by a bunch of microservices like Authorization, Product catalog, and Billing. Now imagine a customer’s checkout process fails, how would you go about determining the cause? You could check the logs of each microservice and eventually find the one with issues. However, this does not scale past a few services.
The more practical approach is to gather the logs from each microservice in a central searchable database. That way when something breaks, you have the complete story easily, hence decreasing your Mean Time To Repair (MTTR).

	##### Example: ```SumoLogic```

	##### Pros: ```Overwhelming size of Logs```

	```Pointers:```

	* Logs are streams of events continuously flowing. Files are inherently static objects. So it is a mismatch of abstractions to store logs in files. This mismatch manifests as an additional complexity of parsing log files to generate useful insights and dealing with file size and rotation policies.

	https://hackernoon.com/monitoring-containerized-microservices-with-a-centralized-logging-architecture-ba6771c1971a

---
* ### **Distributed Tracing:**

	>Distributed tracing, also called distributed request tracing, is a method used to profile and monitor applications, especially those built using a microservices architecture. Distributed tracing helps pinpoint where failures occur and what causes poor performance.

	##### Example: ```Can be acheived by Sleuth```

  
---
## Database Pattern:

  

* ### **Database Per Service:**
---
* ### **Shared Database per Service:**
---
* ### **CQRS:**

	Command Query Resoltion Seperation
---
* ### **SAGA Pattern:**

  

## Integration Pattern:

* **API Gateway Pattern:**

* **Aggregator Pattern:**

* **Proxy Pattern:**

* **Gateway Routing Pattern:**

* **Chained Micoservice Pattern:**

* **Branch Pattern:**

* **Client Side UI Composition Pattern:**
