## 1. Overview
- ==Spring Cloud== is a framework built on top of Spring Boot that provides tools for building distributed systems and microservices architectures. It simplifies the development of scalable, resilient, and cloud-native applications by handling service discovery, configuration management, load balancing, fault tolerance, security, and monitoring.
- As modern applications are increasingly built as microservices, Spring Cloud assists in resolving the related challenges with the architecture type:
	- How do microservices discover each other? (**Service Discovery**)
	- How can they share configurations efficiently? (**Centralized Configuration Management**)
	- How to handle failures gracefully? (**Fault Tolerance & Resilience**)
	- How to distribute requests efficiently? (**Load Balancing**)
	- How to manage security in a distributed environment? (**API Gateway & Security**)
	- How to monitor and trace requests across multiple services? (**Observability & Distributed Tracing**)
- Spring Cloud addresses these challenges by providing opinionated, production-ready implementations of common patterns in distributed systems.
- It is important to note that Spring Cloud is not a part of Spring Boot, but it builds upon it.
	- Spring Boot simplifies the development of Spring applications by providing auto-configuration, embedded servers, and dependency management.
	- Spring Cloud extends Spring Boot by providing solutions for microservices-related concerns like service discovery, configuration management, API gateways, distributed tracing, and security.
- Spring Cloud applications are typically Spring Boot applications with additional dependencies to enable specific microservices features.

## 2. Spring Cloud components & modules
### 2.1. Configuration & Secrets
#### 2.1.1. Spring Cloud Config Server
- ==Spring Cloud Config Server== provides centralized externalized configuration management for distributed systems. It allows services to fetch configuration properties from a common source (e.g., Git, database, Vault, filesystem) rather than having them hardcoded or packaged within the application.
- Manages configs in a single location for multiple microservices. Allows modification of configurations without restarting applications. Environments can be separated into different profiles for dev, staging, prod, etc. Uses Git or Vault to track configuration changes.
- **The way it works:** Config Server fetches configurations from a backend (Git, Vault, JDBC, local files). The microservices (Config Clients) fetch configuration values at runtime. It can use Spring Boot Actuator `/refresh` endpoint to reload updated configs dynamically or can also be combined with Spring Cloud Bus for automatic refresh propagation updates across multiple services.
- **Security considerations:** 
	- The Config Server should not be exposed directly, instead an API Gateway should be used or network security rules.
	- For storing sensitive values, the Spring Cloud Vault should be used or encryption of properties via `spring-cloud-config-server`.
	- The endpoints should obviously be secured with OAuth2, JWT, or at least Basic Auth.

#### 2.1.2. Spring Cloud Vault
- ==Spring Cloud Vault== integrates HashiCorp Vault with Spring applications to securely manage secrets such as database credentials, API keys, and sensitive properties. It helps to avoid storing secrets in plain text.
- Spring Cloud Vault allows dynamic fetching of credentials (e.g., rotate database passwords automatically), fine-grained access control and works with Kubernetes, AWS, and other cloud providers.
- **How it works:** The Spring Boot application requests credentials from Vault Server. The vault validates identity & permission, retrieves the secrets and injects them into Spring's environment. 
- **Security considerations:** 
	- The vault token should never be stored in plain text, but instead in an environment variable.
	- Vault policies should be used to restrict access.
	- Audit logging should be be enabled so that access can be tracked.

### 2.2. Service Discovery
- Service discovery is the mechanism that allows microservices to locate and communicate with each other dynamically, without needing hardcoded IPs or hostnames.
- Two main types of service discovery:
	- Client-Side Discovery - The client determines the location of a service instance from a service registry and makes requests directly. Example: Netflix Eureka, Zookeeper, Consul.
	- Server-Side Discovery - The client makes a request to a load balancer (e.g., Kubernetes Service, API Gateway, or Istio), which then determines the best service instance. Example: Spring Cloud Kubernetes.

#### 2.2.1.  Spring Cloud Netflix Eureka
- ==Spring Cloud Netflix Eureka== is a service registry designed to facilitate service discovery in microservice architectures. It helps services dynamically register themselves and discover other services without relying on hardcoded configurations.
- It has a client-side discovery mechanism where services lookup others from Eureka rather than relying on a centralized load balancer. 
- Works seamlessly with Spring Cloud LoadBalancer (replacing Ribbon) and OpenFeign.
- The use of Eureka eliminates hardcoded service locations as services register dynamically, reducing configuration management complexity.
- Eureka has a resilient design allowing services to function even if the Eureka server is down, as clients cache the service registry locally. It also has self-preservation mode which prevents temporary network issues from removing services.
- Eureka is fully integrated with Spring Cloud and has out-of-the-box support for Feign, LoadBalancer, and Actuator.
- Eureka has its own **downsides**: 
	- Client-side service discovery is not ideal for Kubernetes, which relies on DNS-based discovery. Managing the Eureka server also adds additional maintenance overhead, unlike using built-in Kubernetes Service Discovery.
	- Eureka’s service updates propagate gradually rather than instantly, which may cause temporary stale data and eventual consistency issues.
- **How it works** - Eureka Server acts as a service registry. Eureka Clients (microservices) register themselves and fetch service metadata:
	- Each microservice registers itself with the Eureka Server using its IP/hostname and the Eureka Server maintains a registry of all active instances.
	- When a microservice needs to call another, it queries the Eureka Server and receives a list of available instances and selects one (using a load-balancing strategy like round-robin).
	- Registered services send periodic heartbeats (default: every 30s) and ff a service stops sending heartbeats, Eureka removes it from the registry.
	- If many services stop reporting due to network issues, Eureka does not immediately remove them. This prevents false positives from network partitions.
- **Security considerations**:
	- Use Spring Security and OAuth2 to secure the registry.
	- Restrict which applications can register with Eureka using firewalls or API gateways to mitigate spoofing attacks.
	- Be mindful of data consistency risks as Eureka follows an eventually consistent model rather than strong consistency.
- Eureka is ideal for Spring Boot microservices running outside Kubernetes, using on-premise or VM-based deployments, and offers customizable service discovery with client-side load balancing.
- Eureka should be avoided if using Kubernetes (use Kubernetes-native service discovery instead), there is a strong consistency requirement (Eureka is eventually consistent), and/or server-side discovery is needed (consider API Gateway or Istio).

#### 2.2.2.  Spring Cloud Kubernetes
- ==Spring Cloud Kubernetes== provides seamless service discovery, configuration management, and load balancing for Spring Boot applications running inside a Kubernetes cluster. It leverages Kubernetes’ built-in server-side discovery mechanism, making it the preferred choice for microservices running in Kubernetes.
- Spring Cloud Kubernetes leverages Kubernetes' native service discovery, eliminating the need for an external service registry (like Eureka). Services are automatically registered via the Kubernetes Service API.
- It used DNS-based discovery, based on ClusterIP, NodePort, and LoadBalancer services.
- Spring Cloud Kubernetes enables centralized configuration via the Kubernetes ConfigMaps as well as built-in load balancing, which works with Kubernetes services and Ingress controllers.
- It integrates with Spring Boot, working well with the Spring Cloud LoadBalancer.
- Spring Cloud Kubernetes also has **drawbacks**:
	- It's Kubernetes-dependent, working only inside Kubernetes clusters. This means it also provides less control over load balancing, as it uses Kubernetes' built-in load balancing rather than custom rules.
	- It also makes it more difficult to debug, as it requires `kubectl` and Kubernetes logs for debugging.
- **How it works:**
	- When a Spring Boot application starts inside a Kubernetes pod, it is automatically associated with a Kubernetes Service. The Kubernetes API Server maintains a registry of all available services.
	- A microservice queries another microservice via its service name using Kubernetes' built-in DNS-based discovery. Requests are routed by Kube Proxy or an Ingress Controller to the correct pod.
	- Kubernetes Services provide round-robin load balancing across multiple pod instances.
	- Applications can load properties dynamically from Kubernetes ConfigMaps & Secrets.
- **Security considerations:**
	- Use Kubernetes Network Policies to restrict inter-service communication and Role-Based Access Control to limit which pods can query the Kubernetes API.
	- Sensitive properties should be stored in Kubernetes Secrets rather than ConfigMaps.
- Spring Cloud Kubernetes is ideal for scalable, cloud-native applications, which use Spring Boot microservices running inside Kubernetes. It allows leveraging of Kubernetes' built-in networking & configuration management and eliminates the need for external services.
- It should be avoided if not deploying to Kubernetes and/or a client-side service discovery is needed for custom load balancing.

#### 2.2.3.  Spring Cloud Zookeeper
- ==Spring Cloud Zookeeper== provides service discovery, configuration management, and distributed coordination using Apache Zookeeper. Zookeeper is a distributed coordination service originally designed to manage large-scale distributed systems. It provides strong consistency and leader election capabilities, making it ideal for systems that require distributed locks and high reliability. Spring Cloud Zookeeper integrates Zookeeper’s service discovery and configuration management into Spring Boot applications, allowing services to dynamically register and discover each other without a centralized registry like Eureka.
- Zookeeper ensures strong consistency of services across all nodes (CP in CAP theorem). It also supports leader election, distributed locks, and synchronization. 
- Zookeeper has dynamic service registration and discovery of each other. It also has a built-in failover mechanism, which detects service failures and removes stale service instances.
- Zookeeper is resilient to network partitions, providing high availability in partitioned environments.
- Zookeeper has **disadvantages**:
	- It introduces higher complexity to the architecture due to the requirement of running a Zookeeper cluster.
	- It has the capability to handle hundreds to thousands of nodes but not at Kubernetes scale.
	- It is more resource intensive compared to DNS-based discovery in Kubernetes.
	- It lacks client-side load balancing and unlike Eureka, does not have native Ribbon-style client-side LB.
- **How it works** - Zookeeper follows a hierarchical tree (ZNode) structure for service registration and discovery:
	- Microservice Registers Itself. Zookeeper creates a ZNode (Zookeeper Node) under `/services/my-service`.
	- Zookeeper stores the entry and maintains a list of available instances.
	- A client queries Zookeeper, which fetches service instances by looking under `/services/my-service`.
	- Zookeeper updates changes dynamically. If a service dies, Zookeeper removes the ZNode.
- Zookeeper’s strong consistency model uses a leader-follower (ZAB Protocol) to maintain strong consistency. It elects a leader node to handle writes, while followers replicate changes and ensures that all registered services are always up to date.
- **Security considerations**:
	- Use Digest Authentication to secure Zookeeper nodes.
	- Enable TLS for Zookeeper communication using `ssl.clientAuth` settings.
	- Use Zookeeper quotas to prevent excessive writes from overloading the cluster.
- Zookeeper is ideal for applications requiring strong consistency (leader election, distributed locking) within systems that need high reliability and failover detection. It is also an appropriate choice if you have microservices that you do not want to rely on Eureka or Kubernetes.
- Zookeeper should be avoided if a Kubernetes-native service discovery is needed (use Spring Cloud Kubernetes) and/or there is a requirement for client-side service discovery with flexible load balancing (use Eureka).

#### 2.2.4.  Spring Cloud Consul
- ==Spring Cloud Consul== integrates HashiCorp Consul into Spring Boot applications for service discovery, distributed configuration, and health checking. Similarly to Eureka and Zookeeper, Consul allows dynamic registration and discovery of services, as well as service health monitoring. It also has multi-datacenter support, connecting services across multiple environments.
- Consul is auto-healing - automatically removes unhealthy instances from the service registry.
- Consul stores and manages key-value (KV) pairs for distributed applications with a built-in KV store, providing distributed configuration without requiring Spring Cloud Config.
- Consul supports both DNS and HTTP-based discovery.
- Consul also has a built-in service mesh (Consul Connect), which provides secure service-to-service communication, offering mTLS encryption traffic control, and security policies.
- **How it works** - Spring Cloud Consul integrates with Consul Agent, which runs as a server or client:
	- Microservices register with Consul Agent, storing metadata in Consul’s registry.
	- Other services query Consul using DNS or HTTP API to discover available instances.
	- Consul continuously monitors registered services. If an instance fails, it is removed from the registry.
	- Applications retrieve configuration from Consul KV instead of environment variables or files.
	- If Consul Connect is setup, Consul can provide mTLS-based secure service-to-service communication.
- **Security considerations**:
	- To secure Consul API access, enable ACLs (Access Control Lists) using `consul acl bootstrap`.
	- Use mTLS via Consul Connect to encrypt service communication.
	- Set ACLs on KV Store to restrict access and prevent unauthorized configuration changes.
- Spring Cloud Consul is best for hybrid & multi-cloud deployments as it works across cloud environments. It has dynamic microservice discovery, making it ideal if no client-side logic needed. It also comes with a secure service mesh (which provides mTLS & traffic control) and distributed configuration management, making it a good alternative to Spring Cloud Config.
- Spring Cloud Consul is not a good choice if Kubernetes is already in use (use Spring Cloud Kubernetes), a client-side service discovery method is needed (use Eureka) or service mesh security is not needed (use simpler discovery tools).

#### 2.2.5. Service Discovery Comparison: Eureka vs. Kubernetes vs. Zookeeper vs. Consul

| Feature                       | Eureka                                                                | Kubernetes                                              | Zookeeper                                               | Consul                                                         |
| ----------------------------- | --------------------------------------------------------------------- | ------------------------------------------------------- | ------------------------------------------------------- | -------------------------------------------------------------- |
| **Discovery type**            | Client-Side (applications register & discover services)               | Server-Side (DNS-based) (K8s manages service discovery) | Hierarchical Node-Based (ZNodes store service metadata) | DNS & HTTP-Based (Consul maintains a registry & DNS)           |
| **Health checks**             | Application pings Eureka (self-health reporting)                      | Kubernetes probes (Liveness & Readiness probes)         | Built-in with leader election & automatic removal       | Built-in, performs active health checks                        |
| **Self-healing**              | No automatic removal of dead services (requires client-side eviction) | Automatically removes unhealthy pods                    | Automatically removes dead services                     | Removes failed services automatically                          |
| **Scalability**               | Highly scalable (Netflix scale) but requires manual tuning            | Designed for large-scale container orchestration        | Not very scalable due to Zookeeper's consistency model  | Highly scalable with multi-datacenter support                  |
| **Service registration**      | Manual via client (@EnableEurekaClient)                               | Automatic (K8s registers pods/services)                 | Manual (ZNodes must be created)                         | Automatic (Consul agents handle registration)                  |
| Service discovery             | Applications query Eureka (EurekaClient)                              | Uses DNS-based lookup (kube-dns)                        | Applications query Zookeeper for nodes                  | Applications query Consul via DNS or HTTP API                  |
| **Configuration management**  | Requires Spring Cloud Config                                          | Uses K8s ConfigMaps & Secrets                           | Not natively supported                                  | Built-in Key-Value (KV) store                                  |
| **Multi-data center support** | No built-in multi-region support                                      | Kubernetes services are bound to a cluster              | Limited to single-region clusters                       | Full multi-region, multi-datacenter support                    |
| **Security**                  | No built-in security (must use HTTPS & auth)                          | Uses K8s RBAC, Network Policies, and Pod Security       | No built-in security (must implement auth manually)     | ACLs for access control + mTLS for service-to-service security |
| **Service mesh support**      | No service mesh capabilities                                          | Works with Istio, Linkerd                               | No built-in service mesh                                | Consul Connect (built-in service mesh with mTLS)               |
| **Load balancing**            | Client-side via Ribbon                                                | Server-side (built into K8s Service abstraction)        | Client-side (Zookeeper-aware clients)                   | DNS-based or client-side                                       |
| **Failover handling**         | Relies on client-side retries                                         | Handled by K8s (ReplicaSet, RollingUpdates)             | Built-in leader election & failover                     | Auto-removes unhealthy services, DNS failover                  |
| **Leader election**           | Not applicable (peer-to-peer architecture)                            | No need for leader election                             | Zookeeper has built-in leader election                  | Consul supports Raft-based leader election                     |
| **Persistence model**         | Eventually consistent (AP in CAP theorem)                             | Strong consistency (CP in CAP theorem)                  | Strong consistency (CP in CAP theorem)                  | Strong consistency (CP in CAP theorem)                         |
| **Best for**                  | Standalone microservices (Netflix OSS stack)                          | Kubernetes deployments                                  | Distributed systems requiring strong consistency        | Hybrid cloud, multi-region setups, & service mesh              |
| **Limitations**               | No automatic failover, client-side only                               | Not ideal for non-containerized applications            | Overhead due to strong consistency                      | Higher memory usage & complexity                               |

#### 2.2.6. When to use what

| Use case                                                            | Best choice             | Why                                          |
| ------------------------------------------------------------------- | ----------------------- | -------------------------------------------- |
| Spring Boot Microservices (Non-Kubernetes)                          | Eureka                  | Lightweight, integrates with Netflix OSS     |
| Containerized Microservices in Kubernetes                           | Spring Cloud Kubernetes | Native service discovery, DNS-based          |
| Highly Available Distributed Systems (Leader Election, Consistency) | Zookeeper               | Strong consistency, built-in leader election |
| Multi-Cloud, Multi-Data Center Applications                         | Consul                  | Supports hybrid environments, multi-region   |
| Secure Service Mesh (mTLS, Traffic Control)                         | Consul                  | Built-in Consul Connect service mesh         |
| Auto-Healing & Self-Repairing Services                              | Kubernetes/Consul       | Auto-removes failed instances                |

### 2.3. API Gateway & Traffic Control
- Microservices architectures rely on API gateways and ingress controllers to manage external access, route traffic, enforce security, and handle load balancing.
#### 2.3.1. Spring Cloud Gateway (SCG)
- Spring Cloud Gateway (SCG) is a highly configurable, reactive API gateway that provides dynamic request routing, rate limiting, security, and monitoring for microservices. It acts as a single entry point for external clients, allowing you to offload authentication, caching, and traffic shaping from microservices.
- SCG is reactive & non-blocking, built on Spring WebFlux (Project Reactor), ensuring scalability. It also routes requests dynamically based on service discovery and supports Spring Cloud LoadBalancer for distributing requests across microservices.
- SCG implements rate-limiting via Redis RateLimiter, integrates with OAuth2, JWT, and API key authentication, and also works with Resilience4j to handle service failures gracefully. It also allows pre- and post-processing of requests with global filters.
- SCG integrates with Spring Boot Actuator, Sleuth, and Zipkin for monitoring.
- **How it works:**
	- Clients (Users, Web Apps, Mobile Apps) send requests to SCG.
	- SCG forwards requests to the appropriate microservice, applying filters, authentication, rate limiting and transformations.
	- Microservices process requests and return responses to SCG.
	- SCG sends responses back to clients.

#### 2.3.2. Spring Cloud Kubernetes Ingress
- Spring Cloud Kubernetes Ingress integrates with Kubernetes' native Ingress Controller, allowing microservices in Kubernetes clusters to expose APIs externally. Unlike Spring Cloud Gateway, which runs as a standalone gateway, Kubernetes Ingress uses Nginx, Traefik, Istio, or other controllers.
- It is Kubernetes-native, using built-in Ingress resources to manage traffic and supports DNS-based routing for services, handling secure communication via TLS certificates (TLS & HTTPS support).
- Ingress works with cloud load balancers (AWS ELB, Azure ALB, GCP LB).
- Ingress automatically routes traffic inside the cluster and provides path-based routing, directing requests based on URL patterns.
- **How it works:**
	- Clients (External or Internal) send requests to Ingress Controller
	- Ingress Controller routes requests based on Ingress rules and sends traffic to the appropriate Kubernetes service.
	- The Kubernetes service sends requests to the backend pods.

#### 2.3.3. Spring Cloud Gateway vs. Kubernetes Ingress - Feature comparison

| Feature               | Spring Cloud Gateway                                        | Spring Cloud Kubernetes Ingress                  |
| --------------------- | ----------------------------------------------------------- | ------------------------------------------------ |
| **Architecture**      | Java-based API Gateway running as a microservice            | Kubernetes-native ingress resource               |
| **Technology stack**  | Spring WebFlux (Reactor)                                    | Uses Nginx, Traefik, Istio, or other controllers |
| **Routing mechanism** | Application-level routing (predicate-based)                 | Kubernetes service-level routing (DNS-based)     |
| **Service discovery** | Works with Eureka, Consul, Kubernetes                       | Only Kubernetes-native                           |
| **Load balancing**    | Spring Cloud LoadBalancer, Ribbon                           | Cloud provider LB (AWS, GCP, Azure)              |
| **Rate limiting**     | Built-in (Redis RateLimiter)                                | Not built-in (requires external config)          |
| **Circuit breaking**  | Resilience4j, Hystrix                                       | Not built-in (use Istio for retries)             |
| **Security & Auth**   | OAuth2, JWT, API Key                                        | TLS, OAuth2, JWT via Istio                       |
| **Custom filters**    | Fully customizable pre/post-filters                         | Limited customization (annotations)              |
| **TLS support**       | Custom implementation required                              | Built-in with `cert-manager`                     |
| **Logging & tracing** | Integrated with Sleuth, Zipkin                              | Kubernetes logging (Fluentd, Loki)               |
| **Best for**          | Microservices APIs (fine-grained control, reactive support) | Exposing Kubernetes services to external clients |

#### 2.3.4. When to choose what

| Use case                                           | Best choice          | Why?                                     |
| -------------------------------------------------- | -------------------- | ---------------------------------------- |
| Microservices running outside Kubernetes           | Spring Cloud Gateway | Full control, Java-based                 |
| Microservices running inside Kubernetes            | Kubernetes Ingress   | Native to K8s, uses built-in controllers |
| Need advanced routing, rate limiting, API security | Spring Cloud Gateway | Offers filters, security, custom logic   |
| Need simple URL-based routing for K8s services     | Kubernetes Ingress   | Uses lightweight ingress rules           |
| Running a hybrid architecture (K8s + VMs)          | Spring Cloud Gateway | Works across environments                |
| Using Istio Service Mesh                           | Kubernetes Ingress   | Istio handles routing/security           |


### 2.4. Resilience & Fault tolerance
- ==Circuit breaking== is a design pattern used to detect failures and prevent cascading failures in distributed systems. When a service fails repeatedly, the circuit breaker trips and stops making requests to the failing service for a specified period, allowing it to recover.
- ==Retry handling== is a mechanism to retry failed operations, typically transient failures (e.g., network timeouts, temporary unavailability). It helps improve the chances of success by retrying the operation after a delay.
#### 2.4.1. Resilience4j
- Resilience4j is a lightweight, easy-to-use fault tolerance library designed for Java 8 and functional programming. It is inspired by Netflix Hystrix but is more modern and modular. It provides several core modules: 
	- **Circuit breaker** - A circuit breaker prevents a system from making requests to an unhealthy or failing service for a specified time, allowing it to recover. 
	- **Retry** - Retries a failed request before giving up. Helps with transient failures (e.g., network issues, temporary API downtime).
	- **Rate limiter** - Limits the number of requests per time unit to prevent overload. Used in API rate limiting, preventing excessive load.
	- **Bulkhead** - Restricts concurrent requests to prevent resource exhaustion. Two main types: Semaphore Bulkhead, which limits parallel requests, and ThreadPool Bulkhead, which uses a thread pool to queue excess requests. Used in database connection pooling, protecting critical services from overload.
	- **Time Limiter** - Ensures calls don’t hang indefinitely by setting a max execution time.  Used in preventing slow external calls from affecting system performance.
	- **Cache**
- Resilience4j has modular design (you only import what you need) with a functional programming style, leveraging Java 8’s `Supplier` and `CompletableFuture`, along with asynchronous & reactive support. It works well with Spring Boot via resilience4j-spring-boot.
- **How does circuit breaking work** in Resilience4j:
	- If failures cross a threshold, the breaker opens and blocks requests. After a waiting period, it half-opens and allows limited requests. If those requests succeed, it closes again and resumes normal operation.
	- **Closed State**: The circuit breaker allows requests to pass through. If failures exceed a threshold, it transitions to the Open state.
	- **Open State**: The circuit breaker blocks all requests for a specified duration (`waitDurationInOpenState`). After this duration, it transitions to the Half-Open state.
	- **Half-Open State**: The circuit breaker allows a limited number of requests to test if the service has recovered. If successful, it transitions back to the Closed state; otherwise, it returns to the Open state.
	- **Configuration parameters** for circuit breaker:
		- `failureRateThreshold`: The threshold (in percentage) for failures that will trip the circuit breaker.
		- `waitDurationInOpenState`: The time the circuit breaker stays in the Open state before transitioning to Half-Open.
		- `ringBufferSizeInClosedState`: The number of requests (or percentage) to track in the Closed state.
		- `ringBufferSizeInHalfOpenState`: The number of requests to allow in the Half-Open state.
		- `automaticTransitionFromOpenToHalfOpenEnabled`: Automatically transitions from Open to Half-Open after the wait duration.
	- Example usage:
```
CircuitBreakerConfig config = CircuitBreakerConfig.custom()
    .failureRateThreshold(50)
    .waitDurationInOpenState(Duration.ofMillis(1000))
    .ringBufferSizeInHalfOpenState(2)
    .ringBufferSizeInClosedState(2)
    .build();

CircuitBreaker circuitBreaker = CircuitBreaker.of("backendService", config);

Supplier<String> supplier = () -> backendService.call();
Supplier<String> decoratedSupplier = CircuitBreaker
    .decorateSupplier(circuitBreaker, supplier);

String result = Try.ofSupplier(decoratedSupplier)
    .recover(throwable -> "Fallback response")
    .get();
```
	
- **How does Retry handling work** in Resilience4j:
	- You define max attempts and backoff strategy (fixed, exponential, random delay, etc.). The request is retried until it succeeds or max retries are exhausted.
	- **Configuration parameters**:
		- `maxAttempts`: The maximum number of attempts (including the first call).
		- `waitDuration`: The delay between retries.
		- `retryExceptions`: The list of exceptions that trigger a retry.
		- `retryOnResultPredicate`: A predicate to determine if a result should trigger a retry.
		- `intervalFunction`: A function to calculate the interval between retries (e.g., exponential backoff).
	- Example usage:
```
RetryConfig config = RetryConfig.custom()
	.maxAttempts(3)
    .waitDuration(Duration.ofMillis(500))
    .retryExceptions(IOException.class, TimeoutException.class)
    .build();

Retry retry = Retry.of("backendService", config);

Supplier<String> supplier = () -> backendService.call();
Supplier<String> decoratedSupplier = Retry
    .decorateSupplier(retry, supplier);

String result = Try.ofSupplier(decoratedSupplier)
    .recover(throwable -> "Fallback response")
    .get();
```
	
- **Best practices**:
	- Use retry for transient failures and Circuit Breaker for persistent failures.
	- Use metrics and events to monitor the behavior of Circuit Breaker and Retry mechanisms. Adjust configurations based on observed failure patterns.
	- Always provide fallback responses when using Circuit Breaker or Retry to ensure graceful degradation.


### 2.5. Distributed Messaging & Events in Spring
#### 2.5.1. Spring Cloud Bus
- Spring Cloud Bus is primarily used to propagate configuration changes and broadcast events across microservices. It leverages a distributed message broker (Kafka or RabbitMQ) to ensure real-time synchronization of configurations in a dynamic system.
- Spring Cloud Bus works with Spring Cloud Config to refresh configurations across all instances when a change occurs. It uses `/actuator/bus-refresh` to trigger a global refresh event so instead of refreshing each instance manually, a single request propagates the refresh to all instances.
- SCB allows publishing custom events to synchronize application state across distributed services, using Spring’s event system but extending it across services.
- SCB offer decoupled communication, having no direct service-to-service calls; instead, messages are sent to a broker (Kafka/RabbitMQ) and then broadcasted.
- SCB has auto-Discovery and Propagation so services can listen to specific topics or event types and react accordingly.

#### 2.5.2. Spring Cloud Stream
- Spring Cloud Stream is designed for event-driven microservices using messaging platforms like Kafka and RabbitMQ. It abstracts broker-specific complexities and allows services to publish, process, and consume events seamlessly. It is an abstraction over Kafka and RabbitMQ but it also provides advanced event-processing features like routing, transformation, error handling, and consumer group scaling.
- SCS supports real-time event-driven microservices that react asynchronously to changes. Unlike REST APIs, it avoids synchronous dependencies between services.
- SCP decouples application logic from specific message brokers (Kafka, RabbitMQ) and it uses Spring Cloud Stream Binders to switch between different messaging platforms easily.
- SCP enables Kafka-style consumer groups for scalable event processing and load balances events among multiple service instances.
- SCP supports dynamic routing, serialization, deserialization, and message format transformation, as well as offering automatic retries and dead-letter queue support in case of failures.


### 2.6. Observability & Logging
- Observability in microservices architecture is critical for troubleshooting, performance monitoring, and debugging. It primarily consists of:
	- Logging - Capturing application logs.
	- Tracing - Understanding request flow across services.
	- Metrics - Capturing performance statistics.
#### 2.6.1. Spring Cloud Sleuth
- Spring Cloud Sleuth provides automatic distributed tracing by assigning unique trace IDs to requests and propagating them across microservices. It integrates seamlessly with logging frameworks like Logback and SLF4J.
- Sleuth Automatically propagates trace IDs across HTTP requests, messaging queues (Kafka, RabbitMQ), and scheduled tasks. It adds trace (`traceId`) and span (`spanId`) identifiers to logs, making it easier to track logs related to a specific request.
- A trace represents an end-to-end request lifecycle across multiple services while a span is a single operation within a trace (e.g., a database query or an HTTP call).
- By default, Sleuth samples only a percentage of requests to avoid performance degradation.
- Sleuth supports multiple tracing backends, working with Zipkin, OpenTelemetry, Jaeger, and Brave.
- Sleuth supports Kafka, RabbitMQ, and async tasks. To propagate tracing across async calls, we can use `@Async`. 
- To trace SQL queries, we can use `p6spy` or Spring Sleuth JDBC integration.
- When Sleuth is enabled, logs automatically include `traceId` and `spanId`: `2025-02-25 12:00:01.123 INFO [service-a,traceId=1234567890abcdef,spanId=abcdef1234567890] Starting process request`.
- Example of manually creating traces & spans:
	`@RestController`
	`public class OrderController {`

	    @Autowired
	    private Tracer tracer;

	    @GetMapping("/order")
	    public String createOrder() {
	        Span newSpan = tracer.nextSpan().name("create-order-span").start();
	        try (Tracer.SpanInScope ws = tracer.withSpanInScope(newSpan)) {
	            return "Order Created!";
	        } finally {
	            newSpan.end();
	        }
	    }
	`}`

#### 2.6.2. Spring Cloud Zipkin
- Spring Cloud Zipkin is a distributed tracing system that collects trace data and provides a UI to analyze request flows.
- Zipkin captures traces from Spring Cloud Sleuth and stores them in a database.
- Zipkin allows developers to visualize request paths across microservices, offers support for Elasticsearch, MySQL, PostgreSQL, and in-memory storage, and is useful for debugging slow requests or identifying bottlenecks.


### 2.7. Batch processing & serverless
#### 2.7.1. Spring Cloud Task
- Spring Cloud Task (SCT) is a framework for short-lived, microservice-based batch job execution. It is often used for running one-time, finite, stateful tasks inside a Spring Boot environment.
- Unlike long-running services, SCT is designed for processes that complete and shut down (e.g., ETL jobs, report generation).
- SCT works seamlessly with Spring Boot applications and works well with Spring Batch to manage large-scale batch operations.
- SCT automatically records execution metadata such as start time, end time, exit status in a database.
- SCT also provides event-based execution with hooks for before start, after completion, and error handling.
- SCT is best in use cases for: Running database migrations or ETL jobs, processing one-time tasks, like generating a report, data ingestion from external sources, and launching Spring Batch jobs in a controlled way.
- SCT has four main components:
	- Task Repository - Stores task execution metadata (start time, exit code, etc.).
	- Task Lifecycle Listeners - Hooks that trigger before/after a task runs.
	- Task Execution Table - Automatically created table storing execution logs.
	- Spring Batch Integration - Can start batch jobs as part of task execution.
- Main differences between Spring Cloud Task and Spring Batch:

| Feature                    | Spring Cloud Task                   | Spring Batch                              |
| -------------------------- | ----------------------------------- | ----------------------------------------- |
| **Purpose**                | Runs short-lived microservices      | Runs large-scale batch jobs               |
| **Execution time**         | Typically minutes                   | Can run for hours                         |
| **Persistence**            | Tracks only task execution metadata | Tracks job & step execution               |
| **Retry mechanism**        | Limited (exit codes)                | Advanced (retry policies, restartability) |
| **Transaction management** | Basic                               | Full transaction rollback support         |

#### 2.7.2. Spring Cloud Function
- Spring Cloud Function (SCF) is a framework for serverless, function-based development, allowing you to write cloud-agnostic business logic.
- SCF offers function-based development where business logic is written as `Function<T,R>`, `Consumer<T>`, or `Supplier<T>`. The code is cloud-agnostic allowing deployments to AWS Lambda, Azure Functions, Google Cloud Functions, Knative, etc.
- SCF has seamless Spring Boot integration, as functions run as standalone applications or in a microservice. It also easily integrates with event sources like Kafka, RabbitMQ, HTTP requests, or cloud triggers.
-  SCF supports multiple runtimes (Java, GraalVM, etc).
- It is best used for writing serverless APIs (write and deploy business logic as AWS Lambda/Azure Function), event processing (reacting to events from Kafka, RabbitMQ, AWS SNS, Azure Event Grid), or data processing pipelines (streamlining transformations or filtering in a serverless way).
- Core components of SCF:
	- `Function<T, R>` - Transforms input (T) to output (R).
	- `Consumer<T>` - Processes an input (T) but does not return anything.
	- `Supplier<T>` - Generates an output (T) without input.


### 2.8. Testing & Contract verification
#### 2.8.1. Spring Cloud Contract (SCC)
- Spring Cloud Contract (SCC) is a Consumer-Driven Contract (CDC) testing tool that ensures producers (API providers) and consumers (clients) adhere to a shared API contract.
- SCC has a contract-first approach where API contracts are defined before implementation. The API mocks for consumers are generated using WireMock and the API compliance is verified against the predefined contracts.
- Consumer-driven testing ensures API changes don't break consumers (backward compatibility).
- SCC offers support for HTTP (REST, GraphQL) and Message Brokers (Kafka, RabbitMQ).
- The core components of SCC are the following:
	- Producer - The API provider (e.g., REST service).
	- Consumer - The client using the API.
	- Contract - Defines expected request/response behavior.
	- Stub - A mock service generated from the contract for testing.
	- Consumer-Driven Contract Testing - Consumers define expectations, and the producer verifies them.
- **How it works:**
	- We define a contract by writing an API specification in Groovy/YAML.
	-  SCC generates a WireMock stub.
	- Consumers use the stub in tests.
	- The provider runs contract tests to ensure API correctness.


### 2.9. Big Data & Data Streaming 
#### 2.9.1. Spring Cloud Data Flow (SCDF)
- Spring Cloud Data Flow (SCDF) is a microservices-based toolkit for batch processing and real-time data streaming. It is designed to handle data pipelines, executing batch jobs, and orchestrating event-driven architectures.
- SCDF allows developers to build, orchestrate, and deploy data-intensive applications using Spring Boot microservices. It is often compared to Apache NiFi, Apache Kafka Streams, and Flink, but it focuses on Spring ecosystem integrations.
- SCDF supports real-time event-driven streams and batch job scheduling, as well as building composable pipelines by connecting microservices.
- SCDF has a cloud-native architecture, deploying on Kubernetes, Cloud Foundry, and Docker. It also has pluggable connectors with prebuilt integrations for Kafka, RabbitMQ, JDBC, Redis, and HTTP.
- SCDF also offers a UI for visualizing pipelines, metrics, and logs.
- Spring Cloud Data Flow is a composition of several sub-components:
	- **Spring Cloud Stream (SCS)** - A framework for building event-driven microservices, which uses binders (connectors) for Kafka, RabbitMQ, and others. It defines sources, processors, and sinks for streaming.
	- **Spring Batch** - Framework for batch processing (ETL, database migration, report generation), which supports job scheduling and fault-tolerant batch jobs. It works with Spring Cloud Task for executing short-lived jobs.
	- **Spring Cloud Task** - Runs short-lived microservices (e.g., database cleanup, ETL jobs) and logs job execution details in a database. Works with Spring Batch for one-time jobs.
	- **Spring Cloud Data Flow Server** - Provides REST API for deploying and managing streams & batch jobs. Can be self-hosted or deployed on Kubernetes, Cloud Foundry, or AWS.
- **How Spring Cloud Data Flow Works**:
	- Define the Pipeline:
		- Example: `http | transform | jdbc`
		- This means:
			- `http`: Receive data from HTTP POST.
			- `transform`: Process/clean the data.
			- `jdbc`: Store the data in a database.
	- Deploy microservices using the Spring Cloud Data Flow dashboard or CLI.
	- SCDF tracks logs, metrics, alerts, and supports autoscaling for high-throughput streams.
- SCDF is best for building cloud-native streaming/batch pipelines, orchestrating event-driven microservices, deploying on Kubernetes, AWS, or Cloud Foundry, and managing scalable data processing workflows.
- SCDF should be avoided if looking for low-code ETL (use Apache NiFi instead) and/or if looking for high-performance analytics (use Apache Flink).


1. **Spring Cloud Netflix (Service Discovery & Load Balancing)**
	- Spring Cloud integrates with Netflix OSS to provide key microservices functionalities, however Netflix OSS components have been gradually replaced by other Spring Cloud modules.
		2. **Ribbon (Client-Side Load Balancing)** `[`Replaced by Spring Cloud LoadBalancer`]` – Distributes requests across multiple service instances.
2. **Spring Cloud OpenFeign (Declarative REST Clients)**
	- Simplifies communication between microservices by providing a declarative HTTP client.
	- Instead of using `RestTemplate` or `WebClient`, Feign allows defining APIs as interfaces.
	- Example:
	`@FeignClient(name = "user-service")`
	`public interface UserServiceClient {`
	    `@GetMapping("/users/{id}")`
	    `User getUserById(@PathVariable Long id);`
	`}`
	- The Feign client automatically calls the user-service endpoint without needing manual HTTP calls.


### 2.10. Load balancing
#### 2.10.1. Spring Cloud Load Balancer
- ==Spring Cloud Load Balancer== is a client-side load balancing tool in the Spring Cloud ecosystem. It provides a more lightweight and efficient approach to load balancing for microservices in comparison to its predecessor Ribbon (which is now in maintenance mode).
- When working in a microservices architecture, the client-side load balancer ensures that requests to services are distributed across multiple instances of the service to optimize resource utilization, improve response time, and avoid overloading any single service instance.
- Spring Cloud Load Balancer integrates well with reactive programming (via Spring WebFlux), offering asynchronous, non-blocking load balancing.
- SCLB has built-in mechanisms for fault tolerance, retries, and backoff strategies, allowing the handling of cases where a service instance fails (though Spring Cloud Circuit Breaker or Resilience4J may also be used in conjunction for advanced fault tolerance).
- SCLB uses Round-Robin load balancing as a default strategy, where requests are sent to each available service instance in a circular fashion, ensuring even distribution of requests. However, it also has pluggable load balancer strategies, where we can extend or customize load balancing algorithms to fit the specific use case, such as using weighted load balancing, sticky sessions, or random routing.
- SCLB has integration with Spring Boot auto configuration which helps simplify setup without needing a lot of boilerplate code and it also works with Spring Cloud service discovery tools like Eureka, Consul, Zookeeper, or even custom registries.
- **How it works:**
	- Spring Cloud Load Balancer typically works with a service discovery component like Eureka, Consul, or Zookeeper. It relies on the service registry to find available service instances.
	- The load balancing logic follows various strategies for distributing traffic. The default is Round-Robin, but you can plug in other strategies like Weighted Response Time or Random for different distribution mechanisms.
	- This means that the service client (the caller) has the responsibility of choosing which instance of the service to interact with, based on the load balancing algorithm.
	- When a request is made to a service, Spring Cloud Load Balancer intercepts it and routes it to the best service instance available.
- SCLB is great in a Microservices architecture for simple, client-side load balancing, cloud-native applications, especially in environments like Kubernetes where services might scale dynamically, and reactive systems, using Spring WebFlux that require non-blocking, event-driven architectures.
- SCLB also has its limitations - while it's great for client-side load balancing within a local service environment, for global or cross-region load balancing, you may still need an external solution (e.g., AWS Elastic Load Balancer, NGINX, etc.). It also provides some basic retry mechanisms, for more advanced fault tolerance (circuit breaking, retries, etc.), you may need to use Resilience4J or Spring Cloud Circuit Breaker in conjunction with it.


### 2.11. API Client/Communication:
#### 2.11.1. Spring Cloud OpenFeign
- Spring Cloud OpenFeign is a declarative web service client that simplifies the process of making HTTP requests to RESTful services. It integrates with Spring Cloud to provide load-balanced, fault-tolerant, and scalable service communication. OpenFeign allows the creation of REST clients with minimal code by defining interfaces and annotations, abstracting much of the manual HTTP communication.
- Spring Cloud OpenFeign is built on top of OpenFeign, a Java-based HTTP client library that is simple, flexible, and extensible. Spring Cloud OpenFeign integrates it seamlessly with the Spring ecosystem, including Spring Boot, Spring Cloud Discovery, and Spring Cloud Load Balancer.
- OpenFeign is declarative HTTP client, by using it, we replace the need of manually creating HTTP requests, and instead define Java interfaces and annotate methods with HTTP-specific annotations (`@GetMapping`, `@PostMapping`, etc.). Spring Cloud OpenFeign automatically implements the interface at runtime.
- Spring Cloud OpenFeign has integrations with:
	- Spring Cloud Discovery components (like Eureka, Consul, or Zookeeper) to look up service instances dynamically and load balance requests to them.
	- Spring Cloud Load Balancer to provide client-side load balancing, automatically balancing requests across multiple instances of a service discovered through the service registry.
	- Can be combined with Resilience4J or Spring Cloud Circuit Breaker for fault tolerance. This allows the system to handle failures gracefully and provide fallback logic when services are unavailable.
	- Can be configured to fit various needs such as custom encoders/decoders, timeouts, retries, logging, and error handling strategies.
- OpenFeign also allows customization of HTTP requests, such as custom request headers, modifying request methods, and even executing raw HTTP requests by providing custom configurations or interceptors.
- While OpenFeign makes HTTP client setup easy, it may lack the fine-grained control you would have with more manual HTTP request setups (like `RestTemplate` or `WebClient`), such as more complex authentication schemes or more sophisticated response handling. It also has no direct support for asynchronous calls as it is generally synchronous. To perform asynchronous calls, you would need to use a combination of Spring WebFlux and a non-blocking HTTP client like WebClient.

## 3. Best practices
1. Use centralized configuration management (Spring Cloud Config + Vault)
2. Enable service discovery dynamically instead of hardcoding URLs (Eureka/Consul)
3. Secure microservices with OAuth2 and JWT
4. Use API Gateway (Spring Cloud Gateway) instead of exposing services directly
5. Implement circuit breakers (Resilience4j) to handle failures gracefully
6. Log and trace requests (Spring Cloud Sleuth & Zipkin)
7. Automate configuration refreshes (Spring Cloud Bus + RabbitMQ/Kafka)
8. Deploy to Kubernetes for better orchestration and scaling
