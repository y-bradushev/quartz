Q: What are some common design patterns used in Spring applications?
A: Spring extensively utilizes various design patterns to provide a robust and flexible way to build enterprise-level applications. Here are some of the more common ones:
1) Singleton - Ensures a class has only one instance and provides a global point of access to it. Spring beans are singleton by default, meaning the Spring IoC container creates and manages a single instance of a bean per Spring IoC container.
2) Factory - Defines an interface for creating an object but lets subclasses alter the type of objects that will be created. The Spring IoC container itself acts as a factory capable of creating objects (beans) of any type, depending on the configuration metadata provided by the developer.
3) Prototype - A creational pattern used when the type of objects to create is determined by a prototypical instance, which is cloned to produce new objects. In Spring, beans can be configured to be prototypes, where each request for the bean results in the creation of a new instance.
4) Proxy - Provides a surrogate or placeholder for another object to control access to it. Spring AOP (Aspect-Oriented Programming) uses the proxy pattern extensively to implement cross-cutting concerns (e.g., transactions, security, caching) transparently, without changing the business logic code.
5) Observer - Defines a dependency between objects so that when one object changes state, all its dependents are notified and updated automatically.  The key participants in the observer pattern are:
	1) Subject (Publisher) - the object that generates events and notifies the observers when a significant change occurs.
	2) Observer (Listener) - the object that listens to events published by the subject.
	Spring’s event handling mechanism is built on the observer pattern. It allows application components to publish and listen to application events through a simple application event publisher and listener interface.
6) Template method - Defines the program skeleton of an algorithm in a method, deferring some steps to subclasses. It lets one redefine certain steps of an algorithm without changing the algorithm's structure. Used in Spring's template classes, like JdbcTemplate, HibernateTemplate, etc., which provide a simplified API for various persistence technologies and handle standard operations (e.g., opening and closing connections), while the user is only required to provide the specifics of the operation being performed.
7) Strategy - Enables the exact behavior of a system to be selected either at run-time (dynamic) or compile-time (static). Often, the strategy pattern is used to select the implementation of an algorithm or policy. Spring Security uses the strategy pattern to allow different authentication mechanisms. Similarly, resource resolution in Spring MVC (view resolvers, locale resolvers) can be configured using different strategies.
8) Decorator - Allows behavior to be added to an individual object, either statically or dynamically, without affecting the behavior of other objects from the same class. Spring Web MVC uses the decorator pattern to add responsibilities to HttpServletRequest or HttpServletResponse objects.
9) Builder - Allows for the step-by-step construction of a complex object so that the same construction process can create different representations. Used in the configuration of beans, especially with Java-based config using the @Configuration and @Bean annotations, allowing for fluent API-style configurations.

Q: What is the difference between Factory and Abstract Factory patterns?
A: Both are creational design patterns used in object-oriented design to deal with the process of object creation without specifying the exact class of the object that will be created. The Factory pattern defines an interface for creating an object but lets subclasses decide which class to instantiate, typically involves a single creator class containing a method that returns new instances of a product class. Example:
![[Factory example.png]]
The Abstract Factory Pattern provides an interface for creating families of related or dependent objects without specifying their concrete classes. It involves multiple Factory Methods, one for each type of object to be created. Typically involves an interface or abstract class that defines the methods for creating a range of products. Concrete implementations of this interface or class then implement these methods to instantiate the specific products. Example:
![[Abstract Factory example.png]]

Q: What are the categories of design patterns?
A: There are three main categories of design patterns:
1) Creational - These patterns deal with object creation mechanisms, trying to create objects in a manner suitable to the situation. The basic form of object creation could result in design problems or add complexity to the design. Creational design patterns solve this problem by somehow controlling this object creation. Example patterns are Singleton, Factory, Builder, Prototype.
2) Structural - These patterns deal with how classes and objects are composed to form larger structures. Structural patterns use inheritance to compose interfaces or implementations. This category of design patterns simplifies the structure by identifying the relationships. Example patterns are Adapter, Composite, Proxy, Flyweight, Bridge.
3) Behavioral - These patterns are concerned with algorithms and the assignment of responsibilities between objects. Behavioral patterns describe not just patterns of objects or classes but also the patterns of communication between them. These patterns characterize complex control flow that's difficult to follow at run-time. Example patterns are Observer, Strategy, Command, State, Iterator, Template Method.

Q: Why do we use design patterns?
A: Design patterns provide us with an array of tried and tested solutions to common problems, thus reducing the technical risk to the project, saving time and effort during the implementation stages of the development cycle. Patterns are language neutral so they can be applied to any language that supports object-orientation. By using well understood and documented solutions, the final product will have a much higher degree of comprehension by extension making it easier to maintain as well.

Q: What is the Singleton design pattern?
A: The Singleton Design Pattern is a creational pattern that ensures a class has only one instance and provides a global point of access to that instance. The Singleton pattern can be recognized by a static method that returns the same instance of the class, ensuring that no other instance can be created (typically by intercepting requests to create new instances and returning the singleton instance instead). The pattern restricts the instantiation of a class to a single object, provides a way to access this single object globally, and is usually created lazily, meaning it's created when it's first needed, not when the application starts (to reduce resource usage and improve application startup time). To create a Singleton, we need a private constructor, private static instance variable which holds the instance of the class, and a public static method returning the singleton instance, creating it if necessary. Example:
![[Singleton example.png]]
The basic implementation is not thread-safe however it can be made so if needed. Example:
![[Thread-safe Singleton example.png]]

Q: What is the Prototype design pattern?
A: The Prototype Design Pattern is a creational design pattern that allows an object to create customized copies of itself. It is particularly useful when the creation of an object is more convenient or efficient through duplication of an existing instance rather than through constructing a new instance from scratch. The pattern is typically implemented by defining a prototype interface that includes a method for cloning objects. Concrete classes implement this interface by overriding the cloning method to create a copy of itself. Example:
![[Prototype example.png]]

Q: What is the Builder design pattern?
A: The Builder Design Pattern is a creational pattern that separates the construction of a complex object from its representation, allowing the same construction process to create different representations. This pattern is particularly useful when an object needs to be instantiated with a large number of parameters, some of which may have default values or when the creation process involves several steps that can be configured by the client. Example:
![[Builder example p1.png]]![[Builder example p2.png]]

Q: What is the Proxy design pattern?
A: The Proxy Design Pattern is a structural pattern that provides an object (a proxy) that acts as an intermediary or placeholder for another object (the real subject) to control access to it. This pattern is used to add a layer of abstraction between the client and the actual object, allowing for control over the interaction with the object, enhancing or modifying its behavior without changing the object's code. There are several types of proxies:
1) Virtual proxy - Delays the creation and initialization of expensive objects until needed, acting as a stand-in for objects that are resource-intensive to create.
2) Remote proxy - Provides a local representation for an object that resides in a different address space or network, hiding the details of network communication.
3) Protective proxy - Controls access to sensitive objects by enforcing access control policies.
4) Smart reference proxy - Performs additional actions when an object is accessed, such as reference counting, locking, or loading a persistent object into memory from the database.
Example code for implementing the pattern:
![[Proxy example p1.png]]
![[Proxy example p2.png]]

Q: What is the Flyweight design pattern?
A: The Flyweight Design Pattern is a structural pattern aimed at reducing the memory usage and improving performance in contexts that involve a large number of similar objects. By sharing as much data as possible with related objects (intrinsic state), it minimizes the amount of memory used by sharing as much data as possible. The pattern is particularly useful in scenarios where the instantiation of a high number of objects of a class could lead to a significant memory overhead. In a typical implementation, a factory is used to create and manage the flyweight objects. The factory ensures that flyweights are shared: when a client requests a flyweight, the factory checks if an instance already exists and returns the existing instance; if no such instance exists, it creates a new one. Example:
![[Flyweight example p1.png]]
![[Flyweight example p2.png]]

Q: What is the Saga design pattern?
A: The Saga Design Pattern is a series of patterns designed to manage business transactions that span multiple microservices, ensuring data consistency and integrity across distributed systems. Unlike traditional transaction management, which relies on a two-phase commit (2PC) to maintain consistency, the Saga pattern achieves this in a more flexible and decentralized manner, making it particularly suitable for microservices architectures where services are loosely coupled. A saga is essentially a sequence of local transactions, where each transaction updates data within a single service and publishes an event or message. This event triggers the next local transaction in the saga. If one of the transactions fails, sagas ensure compensation for the previous transactions to maintain data consistency. This compensating action might involve undoing changes made by previous transactions in the saga. There are two primary ways to coordinate sagas:
1) Choreography - In choreographed sagas, each local transaction publishes domain events that trigger the next local transactions. There is no centralized coordination; instead, each service listens for events it's interested in and performs the local transaction and compensation logic as needed. This approach promotes loose coupling but can become complex to manage as the number of services grows.
2) Orchestration - Orchestrated sagas rely on a central coordinator (an orchestrator) that tells the participants what local transactions to execute. The orchestrator keeps track of the saga's progress and instructs each service on its transaction and compensation actions. This approach centralizes control and can simplify understanding the saga's flow but introduces a tighter coupling between services and the orchestrator.

Q: What is the Strategy design pattern?
A: The Strategy Design Pattern is a behavioral design pattern that enables selecting an algorithm's runtime behavior among a family of algorithms. By defining each algorithm in separate classes and making them interchangeable, the Strategy pattern allows the algorithm to vary independently from the clients that use it. This pattern is particularly useful for situations where you need to dynamically change the behavior of an object based on some context or state. To implement the Strategy pattern we declare an interface with methods used by the context to call the algorithm defined by the concrete strategies, we implement the strategy interface for each variant of the algorithm, and we configure a context class that maintains a reference to a strategy object, providing a setter (or constructor) to change the strategy at runtime. Example:
![[Strategy example p1.png]]
![[Strategy example p2.png]]

Q: What is Inversion of Control (IoC)?
A: Inversion of Control (IoC) is a design principle in software engineering where the flow of control or the execution order of a program is inverted compared to traditional procedural programming. In a conventional program structure, the business logic dictates the flow of control through the program, directly creating and managing the lifecycle of dependent objects. IoC, however, delegates the control of object creation and management to an external entity or framework.

Q: What is the difference between Dependency Inversion, Inversion of Control & Dependency Injection?
A: DIP is a guideline that suggests a way to decouple software modules. By following DIP, high-level modules do not depend directly on low-level modules but on abstractions, promoting loose coupling. IoC is a broader principle that encompasses various patterns (including DI) for reversing the control flow in software. It's about removing the responsibility of managing the flow of control from the program itself and letting an external entity manage it. DI is a practical technique under the umbrella of IoC, specifically aimed at managing dependencies between objects. It's a way to achieve IoC by injecting objects with their dependencies from the outside, often facilitated by an IoC container.

Q: What is the Chain of Responsibility design pattern?
A: The Chain of Responsibility pattern is a behavioral design pattern that allows an object to pass a request along a chain of potential handlers until the request is handled. Each handler in the chain has the opportunity to process the request or pass it to the next handler. This pattern decouples the sender of the request from its receivers, allowing multiple objects to handle the request without the sender needing to know which object will handle it. An example use would be handling various types of support tickets in a customer service system where each handler (e.g., Level 1 Support, Level 2 Support, Level 3 Support) can process or escalate the ticket.

Q: What is the Command design pattern?
A: The Command pattern is a behavioral design pattern that encapsulates a request as an object, thereby allowing for parameterization of clients with queues, requests, and operations. It also provides support for undoable operations. This pattern decouples the object that invokes the operation from the one that knows how to perform it.

Q: What is the Adapter design pattern?
A: The Adapter pattern is a structural design pattern that allows incompatible interfaces to work together. It acts as a bridge between two incompatible interfaces by converting the interface of a class into another interface that a client expects. It can be useful when integrating a new system with an existing system where the interfaces are not compatible.

Q: What is the Bridge design pattern?
A: The Bridge pattern is a structural design pattern that decouples an abstraction from its implementation so that the two can vary independently. It involves an abstraction (interface) and an implementation (concrete class), and a bridge (interface) between them. An example use case would be separating GUI abstraction (e.g., Window) from its platform-specific implementations (e.g., Windows, Linux).

Q: What is the Mediator design pattern?
A: The Mediator pattern is a behavioral design pattern that defines an object (the mediator) that encapsulates how a set of objects interact. This pattern promotes loose coupling by preventing objects from referring to each other explicitly and allows their interaction to be varied independently. Example use case would be a chat room application where the mediator (chat room) handles the communication between participants.

Q: What is the Memento design pattern?
A: The Memento pattern is a behavioral design pattern that provides the ability to restore an object to its previous state (undo via rollback). The memento contains the state of an object to be restored later. The components of the pattern are Originator (creates and uses mementos), Memento (stores state), and Caretaker (keeps track of mementos).

Q: What is the Composite design pattern?
A: The Composite pattern is a structural design pattern that allows you to compose objects into tree structures to represent part-whole hierarchies. This pattern treats individual objects and compositions of objects uniformly.

Q: What is the Facade design pattern?
A: The Facade pattern is a structural design pattern that provides a simplified interface to a complex subsystem. It defines a higher-level interface that makes the subsystem easier to use.


