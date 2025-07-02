Q: What is RAD?
A: RAD stands for Rapid Application Development, which is a software development methodology that emphasizes quick and iterative development cycles, active user involvement in the development process, and the use of software and feedback to drive the design of the final product. RAD aims to produce high-quality systems quickly, primarily through the use of component-based development, reusable software components, and prototyping.

Q: How can you share the REST API contracts with someone else?
A: One of the best way to share this information would be to use a tool like Swagger (now known as the OpenAPI specification). It provides a standard, language-agnostic interface to REST APIs, allowing both human and machine access to the information about the service without access to source code, documentation or network traffic inspection. As an alternative, we could also share a Postman collection. 

Q: What is the Same-Origin policy?
A: The Same-Origin Policy (SOP) is a critical security mechanism implemented by web browsers to prevent potentially malicious interactions between web pages from different origins. An origin is defined by the scheme (protocol), host (domain), and port of a URL. According to the SOP, a web page from one origin is restricted from accessing selected resources or scripts hosted on a different origin. For example, JavaScript code on https://example.com can only interact with resources also hosted on https://example.com and not https://another-site.com. While SOP restricts access across origins, technologies like CORS (Cross-Origin Resource Sharing) and others (JSON with padding, PostMessage API, etc.) allow web applications to bypass the SOP by opting in to allow specific cross-origin requests.

Q: What is CORS?
A: Cross-Origin Resource Sharing is policy setting that defines how the browser/application handles cross-origin requests. CORS is a mechanism that uses additional HTTP headers to tell browsers to allow a web application running at one origin to have permission to access selected resources from a server at a different origin.

Q: What are microservices and why do we use it?
A: Microservices is an architectural style that structures an application as a collection of loosely coupled services, which implement business capabilities. Each microservice is a small, independently deployable, and scalable service that runs its own process and communicates with other services through well-defined APIs. Microservices offer several benefits:
1) Modularity - collection of small services focused on specific business functions
2) Scalability - each service can be scaled independently based on demand, without affecting the rest of the application.
3) Technological diversity - microservices can be developed using different programming languages, databases, or other technology stacks.
4) Resilience - the failure of one service doesn't necessarily bring down the entire application.
5) Flexible deployment - microservices can be deployed independently of one another.
While microservices offer significant benefits, they also introduce complexity and challenges, including:
1) Service communication - designing and managing communication between services can be complex, requiring careful API management.
2) Data consistency - ensuring data consistency across services can be challenging, especially with distributed data management.
3) Operational overhead - microservices require robust infrastructure for deployment, monitoring, and management, which can increase operational complexity and overhead.
4) Development and Testing - testing interactions between services adds complexity, and developers must consider network latency, message serialization, and other distributed computing issues.

Q: What are transactions?
A: A transaction is a sequence of operations performed as a single logical unit of work that must either all succeed or all fail together. Transactions are used to ensure data integrity and consistency in the face of errors, crashes, or concurrent operations.

Q: What is the paradigm ACID?
A: ACID is an acronym that refers to the set of 4 key properties that define a transaction:
1) Atomicity - This property ensures that all operations within a transaction are treated as a single unit, which either completely succeeds or completely fails.
2) Consistency - Transactions ensure that the system transitions from one consistent state to another consistent state.
3) Isolation - This property ensures that transactions are properly isolated from each other, preventing concurrent transactions from interfering with each other. Different levels of isolation provide a balance between performance and the degree of isolation, namely Read Uncommitted, Read Committed, Repeatable Read, and Serializable.
4) Durability - Once a transaction has been committed, it is guaranteed that the changes made by the transaction are permanently saved in the system, even in the event of a crash, power loss, or other system failures.

Q: What are the SOLID principles?
A: SOLID principles are a set of guidelines for object-oriented software design intended to make software systems more understandable, flexible, and maintainable. The acronym SOLID stands for:
1) Single Responsibility Principle (SRP) - A class should have only one reason to change, meaning it should have only one job or responsibility. This simplifies maintenance and testing by having each class focus on a single functionality.
2) Open/closed principle (OCP) - Software entities (classes, modules, functions, etc.) should be open for extension but closed for modification. This means you should be able to add new functionality without changing the existing code. This enhances the system's robustness and scalability by allowing it to grow with minimal changes to existing code, reducing the risk of introducing new bugs.
3) Liskov Substitution Principle (LSP) - Objects of a superclass should be replaceable with objects of subclasses without affecting the correctness of the program. Derived types must be completely substitutable for their base types. This ensures that a derived class does not affect the behavior of the base class, thus maintaining the system's reliability and behavior predictability.
4) Interface Segregation Principle (ISP) - No client should be forced to depend on methods it does not use. This principle suggests splitting large interfaces into smaller and more specific ones so that clients only need to know about the methods that are of interest to them. This is done to increase system modularity and prevent the implementation of unnecessary methods in classes, making the system easier to refactor, change, and redeploy.
5) Dependency Inversion Principle (DIP) - High-level modules should not depend on low-level modules. Both should depend on abstractions. Furthermore, abstractions should not depend on details; details should depend on abstractions. This reduces the coupling between different modules or classes, making the system more resilient to change and easier to update or replace components.

Q: What is the layered architecture and why do we use it?
A: Layered architecture, also known as n-tier architecture, is a widely used software design pattern that organizes applications into separate, logical layers, each with a specific responsibility. This architectural style helps in achieving separation of concerns, making applications more manageable, scalable, and modular. It's particularly beneficial in large and complex systems where maintaining code clarity and flexibility is crucial. The core layers are as follows:
1) Presentation Layer (UI layer) - Handles all user interface and browser communication logic. It's where the application's input and output are managed. Technologies include HTML, CSS, JavaScript, frameworks like Angular, React, or server-side rendering technologies.
2) Business Logic Layer (Service Layer) - Contains the core functionality of the application, executing specific business rules associated with the request. This layer acts as a bridge between the presentation layer and the data access layer. Tech includes Java, C#, Python, etc.
3) Data Access Layer (DAL, Persistence Layer) - Manages data persistence and data retrieval operations, abstracting the underlying database or storage mechanism from the business logic layer. Tech includes ORM frameworks like Hibernate, JPA, Entity Framework, direct database access technologies like JDBC, ADO.NET, and database systems like MySQL, PostgreSQL, MongoDB.
4) Database Layer - Stores data needed by the application. This layer could involve relational databases, NoSQL databases, file systems, or other forms of data storage. Tech includes SQL databases (e.g., MySQL, PostgreSQL), NoSQL databases (e.g., MongoDB, Cassandra), cloud storage services.

Q: What is authentication?
A: Authentication is a security process that verifies the identity of a user, device, or entity attempting to gain access to a system, network, or application. Authentication can be performed using various methods, often categorized into types or factors:
1) Password or PIN (Personal Identification Number)
2) Authenticator app or hardware token - 2FA
3) Biometric authentication - fingerprint, facial recognition, iris scanning, voice recognition
4) Location authentication - IP address or GPS
5) Behavioral biometrics - typing rhythm, mouse movements, interaction patterns

Q: What is authorization?
A: Authorization is a security process that determines access rights and privileges for users, systems, or entities to resources, data, and functions within a computing environment. It occurs after successful authentication, once the system has verified the user's identity. While authentication answers the question "Who are you?" by verifying credentials like usernames and passwords, authorization answers "What are you allowed to do?" by determining the resources and operations accessible to the user.

Q: What is serialization?
A: Serialization is the process of converting an object's state into a format that can be stored or transmitted and subsequently reconstructed. The format used for serialization can be binary, text (such as XML or JSON), or another custom format. On the inverse, deserialization is when you convert the serialized data back into an object with the same state as the original.

Q: What is a framework?
A: A framework in software development is a comprehensive set of code libraries and a supporting structure, where developers can build and deploy applications. It provides a foundation and predefined architecture to streamline the development process, offering generic functionality which can be selectively overridden or specialized by writing user-defined code. Frameworks dictate the flow of control through inversion of control (IoC), meaning they call the developer's code rather than the developers calling the framework's code.

Q: What is a library?
A: A library in software development is a collection of precompiled routines, functions, or classes that a program can use to accomplish specific tasks. Essentially, it's a set of reusable code that developers can call upon to perform common operations without having to write the code from scratch.

Q: What are dependencies and dependency management?
A: Dependencies in software development refer to external code libraries or modules that an application needs to function properly. In essence, if a piece of software relies on external code or services to operate, those external elements are considered its dependencies. Dependency management tools automate the process of integrating external libraries or modules into software projects. They help developers add, remove, and update dependencies more efficiently. Tools like Maven and Gradle manage project dependencies, build processes, and deployment mechanisms. They use pom.xml (Maven) or build.gradle (Gradle) files to declare project dependencies.

Q: What is an API?
A: An API (Application Programming Interface) is a set of rules, protocols, and tools for building software and applications. It specifies how software components should interact and allows different software entities to communicate with each other. APIs define the methods and data formats that developers can use to interact with the program, operating system, or other services.

Q: What is the client-server model?
A: The client-server model is a distributed application structure that partitions tasks or workloads between providers of a resource or service, known as servers, and requesters of a service, known as clients. The communication follows the request-response model where servers host resources and services, such as files, web pages, or application access, in a centralized manner, while clients connect to these servers to request and consume the resources. Typically, the communication between client and server is asynchronous, meaning the client sends a request to the server and continues with its process until the server responds.

Q: What is the request-response model?
A: The request-response model is a communication pattern widely used in computing, particularly in client-server architectures and web development, where a client sends a request to a server, which processes the request and returns a response. This model underpins much of the interaction on the internet and is fundamental to web protocols like HTTP.

Q: What is HTTP?
A: HTTP (Hypertext Transfer Protocol) is a foundational protocol used by the World Wide Web to define how messages are formatted and transmitted, and what actions web servers and browsers should take in response to various commands. HTTP follows the client-server model, where a client (usually a web browser) sends an HTTP request to the server, which then returns an HTTP response. HTTP is stateless, meaning each request from a client to a server is treated as independent; the server does not retain session information between requests. However, mechanisms like cookies, sessions, and tokens are used to maintain state across requests. HTTP responses include status codes that indicate the result of the request. HTTP messages (requests and responses) include headers that contain metadata about the message, such as content type, content length, server information, and caching policies.

Q: What are HTTP headers and what they are used for?
A: HTTP headers are components of the header section of request and response messages in HTTP. They define the operating parameters of an HTTP transaction. Headers are key-value pairs sent between the client and server, which provide essential information about the data being transferred, the request or response, or the client or server itself. HTTP headers are categorized into four main types: General headers, Request headers, Response headers, and Entity headers.

Q: What are the HTTP status code groups?
A: If the URL is valid and the connection is granted, the server will send the client the requested resources and a status code, which are as follows:
1) 1xx - informational - request is received, process continues
2) 2xx - successful - request was successfully received, understood, and accepted
3) 3xx - redirection - further action needs to be taken in order to complete the request
4) 4xx - client error - request contains bad syntax or cannot be fulfilled
5) 5xx - server error - server failed to fulfill an apparently valid request

Q: Explain Bubble Sort
A: Bubble Sort works by comparing each pair of adjacent items and swapping them if they are in the wrong order. This process is repeated from the beginning of the list until no swaps are needed, which means the list is sorted. The algorithm gets its name because smaller elements "bubble" to the top of the list (beginning of the array) with each iteration, while the larger elements sink to the bottom (end of the array). The efficiency of Bubble Sort is low for large datasets because its average and worst-case complexity are both quadratic. However, it has a small footprint, making it somewhat useful for small datasets or when memory space is limited. The best case for the algorithm is O(n) if the array is already sorted, and the algorithm only needs to make one pass through the array to confirm it. Worse case is O(n^2) when the array is sorted in reverse order, requiring the algorithm to compare and swap every element multiple times. Example implementation: 
![[Bubble sort example.png]]

Q: Explain Merge Sort
A: Merge Sort works by recursively splitting the array into halves until each subset contains a single element. Arrays with one element are by definition sorted. Then, it repeatedly merges these arrays together with a merge process that ensures the resulting merged array is also sorted. This process continues until we get a single sorted array. This merge step is key to the algorithm’s efficiency, making sure that as it combines smaller sorted arrays into larger ones, the entire structure remains sorted. It’s not an in-place sorting algorithm because it requires additional space proportional to the input size for the merging process, but it’s very efficient for large datasets. Best case for the algorithm is O(n log n) when it consistently divides the array in half and then merges the arrays, which requires linear time in the size of the array. Worst case is again O(n log n) because the performance of merge sort is stable regardless of the initial order of the input. Example implementation:
![[Merge sort example p1.png]]
![[Merge sort example p2.png]]

Q: Explain Quick Sort
A: Quick Sort begins by selecting a pivot element from the array. The choice of the pivot can vary; it can be the first element, the last element, the median, or a random element. After the pivot is selected, the array is partitioned so that elements less than the pivot are moved before it, and elements greater than the pivot are moved after it. This partitioning step ensures that the pivot element is in its final sorted position. The algorithm then recursively applies the same process to the sub-arrays formed by dividing the array at the pivot. The recursion is applied to the sub-arrays with elements less than and greater than the pivot. The process repeats until the base case of a sub-array with fewer than two elements is reached, at which point the array is sorted. Best case is O(n log n) when the pivot divides the list into two equal halves, leading to the fastest sorting. Worst case is O(n^2) when the pivot element is consistently the smallest or largest element of the list, leading to one side of the partition being empty and forcing the algorithm to make n−1 recursive calls. Example implementation:
![[Quick sort example p1.png]]![[Quick sort example p2.png]]

Q: Explain Heap Sort
A: Heap Sort works in two major phases. In the first phase, it builds a heap out of the input array. This can be done in-place for an array representation of a heap, ensuring that the highest (max heap) or lowest (min heap) value is at the root of the heap. In the second phase, the algorithm repeatedly removes the root element (the maximum element, in the case of a max heap) from the heap, and then reheapifies (restructures the heap to maintain the heap property) the remainder of the heap. This removed root element is placed into the sorted section of the array. This process repeats until all elements have been removed from the heap and placed into the array, resulting in a sorted sequence. The efficiency of Heap Sort comes from the fact that it combines the best aspects of both insertion sort and merge sort; like insertion sort, it can sort "in-place" requiring no additional memory, and like merge sort, it has O(n log n) performance. Best or worst case, it's always O(n log n)  because it always requires a fixed number of steps to sort the elements, regardless of their initial arrangement. Example implementation:
![[Heap sort example p1.png]]![[Heap sort example p2.png]]

Q: Explain TimSort
A: TimSort divides the array into small segments that it sorts using insertion sort, choosing this algorithm for its efficiency on small datasets. The size of these segments, or "runs," varies depending on the size of the array but is chosen to optimize performance. After sorting these small segments, TimSort then merges them together in a manner similar to merge sort, taking advantage of the fact that these segments are already sorted to minimize the number of comparisons and swaps needed. The algorithm adaptively increases the size of the runs it merges as it progresses through the array, further optimizing its performance. It also employs a technique called "galloping" to expedite the merging process when one run's elements are much smaller or larger than the other's. Best case is O(n) when the data is already sorted or nearly sorted while worst case is O(n log n) due to its hybrid strategy. For an implementation, we can check the OpenJDK or CPython implementations. 

Q: What strategies would you use to ensure a Java web application is secure from common vulnerabilities?
A: Answers might include implementing secure authentication and authorization, validating and sanitizing input data, using HTTPS, preparing for SQL injection and XSS attacks, and regular security testing with tools like OWASP ZAP.

Q: Explain the difference between Agile and Waterfall software development methodologies
A: 
1) Agile is an iterative and incremental approach to software development that emphasizes flexibility, collaboration, and customer feedback. Key features include short development cycles called sprints, continuous integration, and regular stakeholder involvement. Agile methodologies include Scrum, Kanban, and Extreme Programming (XP). Agile provides adaptability to changing requirements, improved customer satisfaction, and frequent delivery of functional software but requires strong team collaboration and may be challenging to scale for large projects.
2) Waterfall is a linear and sequential approach where each phase of the development process must be completed before moving on to the next. The phases typically include requirements, design, implementation, testing, deployment, and maintenance. Advantages are the clear structure and documentation, easier to manage for small projects with well-defined requirements but it is inflexible to changes once the process has started, potential for delays if any phase takes longer than expected.

Q: What is Continuous Integration (CI) and Continuous Deployment (CD)?
A: CI is a development practice where developers integrate code into a shared repository frequently, typically several times a day. Each integration is automatically tested to detect errors quickly. Tools like Jenkins, Travis CI, and CircleCI are commonly used. CD extends CI by automatically deploying every change that passes the automated tests to production. This practice ensures that the software is always in a releasable state.

Q: What is the DRY design principle?
A: DRY stands for Don't repeat yourself. This principle emphasizes reducing duplication in code by ensuring that each piece of knowledge has a single, unambiguous representation within a system.

Q: What is the KISS design principle?
A: KISS stands for Keep it Simple, Stupid. This principle advocates for simplicity in design and implementation, avoiding unnecessary complexity.

Q: What is the YAGNI design principle?
A: YAGNI stands for You Aren't Gonna Need it. This principle advises against adding functionality until it is necessary.

Q: Explain RESTful API design principles.
A: 
1) Statelessness: Each request from a client to a server must contain all the information needed to understand and process the request. The server does not store client context between requests.
2) Resource-based URIs: URIs should identify resources (nouns) rather than actions (verbs). For example, /users for accessing user resources.
3) Standard HTTP Methods: Use standard HTTP methods (GET, POST, PUT, DELETE) to perform operations on resources. GET retrieves data, POST creates new resources, PUT updates existing resources, and DELETE removes resources.
4) Use of HTTP Status Codes: Return appropriate HTTP status codes (e.g., 200 OK, 404 Not Found, 500 Internal Server Error) to indicate the result of the API call.

Q: What is the difference between REST and GraphQL?
A: 
1) REST (Representational State Transfer) is an architectural style that uses standard HTTP methods to interact with resources identified by URIs. RESTful APIs are stateless and typically return JSON or XML responses. It is simple to implement and understand, leverages standard HTTP protocols. However it has disadvantages in over-fetching or under-fetching data, multiple endpoints for different resources.
2) GraphQL is a query language for APIs that allows clients to request exactly the data they need. It provides a single endpoint and enables clients to specify the structure of the response. It provides efficient data fetching, reduces the number of API calls, flexible query structure. However, it is more complex to set up, and requires careful query management to avoid performance issues.

Q: What are web security best practices?
A: 
1) Input Validation: Validate and sanitize all user inputs to prevent injection attacks.
2) Use HTTPS: Ensure all data transmitted between clients and servers is encrypted using HTTPS.
3) Prevent SQL Injection: Use parameterized queries or ORM frameworks to prevent SQL injection.
4) Protect Against XSS: Sanitize user inputs and use security headers to prevent Cross-Site Scripting (XSS) attacks.
5) Authentication and Authorization: Implement robust authentication mechanisms (e.g., OAuth2) and enforce proper authorization checks.
6) Regular Security Testing: Perform regular security testing using tools like OWASP ZAP or Burp Suite to identify and fix vulnerabilities.

Q: What is a microfrontend?
A: Microfrontends extend the microservices concept to the frontend, allowing different teams to develop and deploy frontend components independently. Each team can choose its own technology stack and manage its part of the application as a separate entity.
