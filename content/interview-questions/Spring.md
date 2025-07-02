Q: What is the difference between @Component and @Bean annotations?
A: There are several main differences between the two annotations, namely:
1) Purpose/role - @Component is a class level annotation while @Bean is a method-level annotation for creating beans.
2) Typical use - @Component represents a general-purpose bean while @Bean is used to explicitly declare individual beans.
3) Creation - @Component is automatically created and managed by Spring while @Bean is explicitly defined in a configuration class.
4) Component scan - @Component is detected and register during component scanning while @Bean is not detected during component scan, it requires explicit @Configuration
5) Dependency injection - @Component can be injected into other components through Spring's IoC container while @Bean can also be injected into other components but the method returning the bean is invoked
6) Lifecycle - Spring manages the complete life cycle of @Component and it manages the lifecycle of @Bean but individual methods in a @Configuration class are not managed by Spring

Q: What are Spring annotations?
A: Annotations in Spring serve as metadata for the classes, methods, and variables to which they are applied, informing the Spring container how to manage and wire these components at runtime. This allows the behavior to be configured within the Java codebase, eliminating the need for separate XML or other configuration files.

Q: What is the difference between @Component, @Service, @Repository, and @Controller annotations?
A: Aside from labelling purposes, there are some functional difference in how Spring treats the different annotations. They mainly pertain to aspect-oriented programming, exception handling, and specific behaviors:
1) @Repository - when you use @Repository on a class, Spring will provide automatic translation of exceptions thrown by the data access technology used (like JPA, Hibernate, JDBC) into a consistent set of Spring's data access exceptions. This allows for cleaner error handling in the data access layer without coupling the code to specific database exceptions.
2) @Controller - the @Controller annotation marks the class as a web controller, capable of handling HTTP requests. Spring MVC provides model binding, request parameter binding, and other web-related functionalities specifically for these annotated classes. Additionally, @Controller classes can use other annotations like @RequestMapping, @GetMapping, etc., to map URLs to class methods directly. Spring MVC will not process these request mapping annotations unless they are on a class annotated with @Controller (or @RestController, which is a specialized version of @Controller that includes @ResponseBody to indicate that the return value of the method should be bound to the web response body).
3) @Service - while @Service does not introduce specific behavior over @Component, it is intended to mark a service layer component. The use of @Service over @Component can be useful for targeting pointcuts in aspect-oriented programming (AOP), where you might want to apply specific behavior (like transaction management) only to service-layer components.
4) @Component - this is a generic stereotype for any Spring-managed component, but it doesn't provide any additional behavior over what a Spring bean would have. It's useful for automatic component scanning and bean instantiation. 

Q: What is the difference between a path variable and a query parameter?
A: Path and query parameters are both ways to pass information to a server as part of an HTTP request, commonly used in web APIs and services.
1) Path variables - used to identify a specific resource or resources. They are part of the URL's path. For example, in the URL http://example.com/api/users/123, the number 123 is a path parameter that might be used to identify a specific user within the users collection.
2) Query parameters - used to provide optional modifications to the request or to query a resource with more detail. They appear at the end of the URL after a question mark (?) and can be chained together using ampersands (&). For example, http://example.com/api/users?age=25&country=Canada uses query parameters to filter users by age and country.

Q: When should @PathVariable and @RequestParam annotations be used?
A: @PathVariable is used for essential parameters that form part of the URL's path, especially when the parameter is used to identify a specific resource or a group of resources in RESTful APIs. @RequestParam is used for more flexible query parameters that are not essential for the request to succeed but modify the request's outcome. It's suitable for optional parameters, filtering data, or when you need to provide default values for parameters.

Q: What are custom annotations in Spring and what are they used for?
A: Custom annotations in Spring are user-defined annotations that can be created to provide metadata and logic to classes, methods, or fields. They are used for a wide range of purposes, such as to inject behavior, apply configuration, or enforce validation rules in a declarative manner. You might use custom annotations in Spring to achieve cross-cutting concerns in a cleaner way, like logging, security, transaction management, or to simplify Spring bean configuration. 

Q: How to create and process a custom annotation in Spring?
A: You define a custom annotation using the @interface keyword in Java. You can specify elements (with default values if needed) that act as the configuration parameters for the annotation. Additionally, you use meta-annotations (annotations on your custom annotation) such as @Target and @Retention to specify where your annotation can be applied and how long the annotation information is kept. To process a custom annotation, you typically use Spring's AOP capabilities or handle the annotation directly in your code. For AOP, you define an aspect that includes advice (methods) annotated with @Before, @AfterReturning, @AfterThrowing, or @Around, targeting your custom annotation. Alternatively, you can process the annotation programmatically by using reflection to inspect elements annotated with your custom annotation and applying logic accordingly.

Q: When would a custom annotation be used over a standard one in Spring?
A: Custom annotations are particularly useful when you have application-specific behavior that standard annotations cannot express or when you want to consolidate multiple annotations and configurations into a single, cohesive annotation. For instance, if you have a common security requirement or logging pattern that applies to multiple methods across services, instead of repeating standard annotations and configurations, you could create a custom annotation that encapsulates this common behavior.

Q: What does spring.jpa.hibernate.ddl-auto application property do?
A: The value under this property influences how the schema tool management will manipulate the database schema at startup. The possible values are:
1) create - drops existing tables and creates new ones
2) update - the object model created based on mappings (usually annotations, but can be XML too) is compared with the existing schema, if necessary, new columns, constraints, etc. will be added but existing ones are never deleted
3) create-drop - similar to create but the tables are dropped after all operations are completed
4) validate - only validates whether the tables and columns exist, otherwise, an exception is thrown

Q: What is logging in Spring Boot?
A: Logging is a mechanism for recording information about the application's runtime behavior. It's a crucial aspect for debugging, monitoring, and auditing the application. It uses Logback as the default logging framework but supports others like Log4J2 and JUL (Java Util Logging). The default log level is INFO, but it can be adjusted. Beyond console logging, Spring Boot can be configured to write logs to files, with support for log file rotation and maximum file size policies. Spring Boot supports various log levels (TRACE, DEBUG, INFO, WARN, ERROR, FATAL, OFF), allowing developers to specify the granularity of information logged.

Q: What are the logging levels in Spring Boot?
A: Logging levels are used to categorize the severity and purpose of log messages. There are several common logging levels:
1) Trace - Provides the most detailed information. Expect these logs to be highly verbose.
2) Debug - Provides detailed information on the flow through the system. Less granular compared to TRACE.
3) Info - Indicates general events that highlight the progress of the application. Suitable for recording significant application events, startup or shutdown sequences, and other operational highlights during normal operation.
4) Warn - Indicates potentially harmful situations. Useful for logging issues that are not errors but could potentially lead to errors or degrade application performance or user experience.
5) Error - Highlights error events that might still allow the application to continue running. Appropriate for logging errors that affect the current operation but do not necessarily stop the application from running, such as failing to load a specific resource.
6) Fatal (also known as Critical in certain frameworks) - Very severe error events that will presumably lead the application to abort. Used to log critical issues that require immediate attention, such as a severe system failure that prevents the application from continuing to run.
7) Off - The highest possible rank and is intended to turn off logging. Used to disable logging entirely, either globally or for specific parts of an application.

Q: What are the elements of a logging framework?
A: Every logging framework typically consists of at least three elements:
1) Logger - primary interface that developers use in their code to log messages. It's responsible for capturing logging information and sending it to the appropriate destination(s). They can be hierarchical, inheriting configurations from their parents, which allows for fine-grained control over log output based on package or class name.
2) Handler (or Appender) - Appenders define where log messages are outputted. Different appenders can send log messages to various destinations like console, files, databases, or external systems. A single logger can be configured with multiple appenders, allowing messages to be routed to different outputs. Appenders also support filtering, formatting, and other custom behaviors.
3) Formatter (or Layout) - this component defines the format or structure of the log message output. It dictates how log messages are presented or serialized. Formatters can include or exclude certain pieces of information (e.g., timestamp, thread name, logger name, level) and can format messages in various ways, including plain text, XML, JSON, or custom formats.

Q: What is the Inversion of control (IoC) concept in Spring?
A: IoC is a design principle where the control of objects or portions of a program is transferred to a container or framework. Traditionally, in tightly coupled frameworks or libraries, the flow of control is dictated by the framework itself, and the user's code is called by the framework. IoC inverts this control, whereby the framework calls the code provided by the user. This approach leads to a more modular, extensible architecture. In the Spring Framework, IoC is achieved through the Spring IoC container. The container manages the lifecycle and configuration of application objects, known as Spring beans. The container creates these beans, wires them together, configures them as specified, and manages their complete lifecycle.

Q: What is the Dependency Injection (DI) concept in Spring?
A: DI is a design pattern used to implement IoC. It allows the creation of dependent objects outside of a class and provides those objects to a class in different ways. Instead of having your objects create their dependencies (tight coupling), or even start up a factory to create them, dependencies are "injected" into the object when it's created by some external entity, typically an IoC container. Spring provides a powerful IoC container that makes extensive use of DI. The Spring container reads the configuration metadata provided by the developer (via XML, annotations, or Java config) and uses this information to manage the beans (objects) within the application context. This management includes the instantiation of objects, wiring them together by setting their properties or constructor arguments, and managing their lifecycle.

Q: What are the different types of Dependency Injection in Spring?
A: Spring supports several types of dependency injection:
1) Constructor injection - Dependencies are provided through the class constructor. Best for mandatory dependencies and when aiming for immutability. It's the recommended approach for most cases as it makes clear what dependencies are required and ensures that the object is always created in a fully initialized state.
2) Setter injection - Dependencies are set through setter methods or other configuration methods after the object has been constructed. Useful when dealing with optional dependencies or when there's a need for reconfiguration or re-injection of dependencies after the object has been created. However, it can lead to partially initialized objects if not used carefully.
3) Field injection - Dependencies are injected directly into the fields of a class. Convenient for quickly wiring classes without writing constructors or setters. Typically used in tests and configurations. However, it's generally discouraged for production code due to difficulties in testing, potential for circular dependencies, and challenges in achieving immutability.
4) Method injection - A general form where a custom method is annotated for injection, different from a typical setter method. It's also used in advanced scenarios like looking up prototype beans within a singleton bean. Useful for advanced configurations where specific logic needs to be executed during the injection process. Also, it's used for injecting prototype scope beans into singleton scope beans to provide a new instance each time the bean is accessed.

Q: What is Spring Boot?
A: Spring Boot is an extension of the Spring Framework that simplifies the process of developing robust Spring applications. It's designed to minimize the amount of configuration and setup needed to get a Spring application up and running. It provides several key features:
1) Auto-configuration - Automatically configures your Spring application based on the jar dependencies you added. For example, if Spring Boot detects spring-webmvc, it will configure your application as a web application.
2) Standalone - Spring Boot applications are standalone and do not require an external web server. They include an embedded web server (like Tomcat, Jetty, or Undertow) that can be easily started with your application.
3) Opinionated defaults - Comes with a set of pre-defined configurations (opinionated defaults) to reduce the effort needed to configure Spring applications. These defaults are good for many projects out of the box, but can be overridden when necessary.
4) Spring Boot starters - These are a set of convenient dependency descriptors that you can include in your application. Each starter is designed to simplify the Maven or Gradle configuration by including the necessary dependencies to get a specific feature working.
5) Actuator - Provides a series of built-in endpoints that can be used to monitor and manage your application, such as health checks, metrics, and application environment information.

Q: What is the difference between Spring Boot and Spring?
A: There are several key differences between the two:
1) Simplification and automation - Spring Framework provides a comprehensive programming and configuration model for modern Java-based enterprise applications, with support for dependency injection, aspect-oriented programming, transaction management, and more. However, setting up a Spring application from scratch requires a fair amount of configuration. Spring Boot aims to simplify the use of the Spring Framework by providing a more streamlined development experience. It automates much of the configuration and setup process, allowing developers to focus more on business logic.
2) Embedded web server - With Spring Framework, you typically need to deploy your web application to an external web server. Spring Boot, however, includes embedded web server options (Tomcat, Jetty, or Undertow), making it easy to develop and test web applications without external setups.
3) Project initialization - Spring Framework applications often require manual setup and configuration of project structure, dependencies, and configurations. Spring Boot provides tools like the Spring Initializr web interface that lets you quickly bootstrap a new project by selecting the necessary configurations and generating the project structure for you.
4) Actuator for Management and Monitoring - Spring Boot includes the Actuator module for application monitoring and management, which is not inherently a part of the traditional Spring Framework setup.

Q: How do you configure a database connection in Spring Boot?
A: There are two main ways to configure a database connection in Spring Boot:
1) Using the application.properties file - the connection properties can be specified in the file, and Spring Boot will automatically configure the database connection
2) Using a @Configuration class - A @Configuration class can be created and there we can specify the connection properties. Spring Boot will then use the @Configuration class to configure the database connection.

Q: What is the purpose of the @SpringBootApplication annotation?
A: The @SpringBootApplication annotation is a convenience annotation that adds the: 1. @EnableAutoConfiguration annotation, which tells Spring Boot to automatically configure your application based on the dependencies present on the classpath; 2. @ComponentScan annotation instructing Spring to search the package of the annotated class and its sub-packages for components, configurations, and services to register them as beans in the Spring context so that there is no need to manually register each component; 3. @Configuration allowing the annotated class to serve as a configuration class as well. This means the class can define additional bean definitions and configurations if needed.

Q: What are some of the common caching techniques used in Spring Boot applications?
A: Caching is a crucial technique for enhancing application performance and scalability by temporarily storing copies of data or computed results to serve future requests more rapidly. Various caching strategies can be used:
1) In-Memory Caching: Stores data directly in the application's memory for quick access. It's the fastest form of caching but is limited by the application's memory capacity.
2) Disk Caching: Persists cached data to disk. It's slower than in-memory caching but suitable for large datasets that exceed memory limits and require persistence across application restarts.
3) Distributed Caching: Involves multiple cache instances across a network, sharing data among different application instances or services. It's ideal for high availability, fault tolerance, and scalability in distributed systems. 
4) Page Caching: Refers to caching the entire output of a web page so that it can be served without reprocessing. This approach is effective for reducing server load and improving response times for static or semi-static web pages.
5) Query Caching: Caches the result set of database queries. By avoiding repeated database hits for the same query, it significantly reduces database load, improving performance. Implementing query caching often involves configuring the ORM layer, such as Hibernate's second-level cache, to store and retrieve query results.

Q: What is the significance of the @Autowired annotation in Spring Boot?
A: The @Autowired annotation in Spring Boot (and Spring Framework in general) plays a crucial role in the framework's dependency injection feature. It marks a point of injection to let Spring know that a bean needs to be injected into another bean without requiring explicit configuration. By annotating a field, constructor, or setter method with @Autowired, Spring Boot automatically resolves and injects the appropriate dependency into the annotated component. It promotes loose coupling between components and lets Sprint Boot take care of resolving and injecting the required dependencies at runtime.

Q: What are profiles in Spring Boot and how can they be used?
A: Profiles provide a way to segregate parts of your application configuration and make it available only in certain environments. Profiles allow you to define sets of configuration that are only activated under specific named profiles, enabling the tailoring of an application for different environments without changing the actual code. Key uses: 1. You can define different configurations for various environments (dev, test, prod) within the same application; 2. Beans can be conditionally registered based on the active profile, allowing you to use different implementations or configurations of a bean depending on the current environment; 3. Profiles make it easier to switch between different configurations without altering the code, simply by changing the active profile(s).

Q: How do you segregate environment specific properties in Spring Boot?
A: Spring profiles provide a way to segregate parts of the application configuration and make it available only in certain environments. For example, we can use it to configure different databases at runtime based on the specific environment set by the profiles.

Q: What is the @PropertySource annotation used for?
A: The @PropertySource annotation in Spring Framework is used to declare a source of properties that Spring's Environment can use. This annotation provides a convenient way to add property files to Spring's Environment, making the properties defined in those files available to the application. It's particularly useful for specifying configurations that are external to your application and for organizing configuration properties into separate files based on their purpose or usage.

Q: How to configure CORS in Spring Boot?
A: The Cross-Origin Resource Sharing policy can be configured in several ways:
1) Global configuration through the "WebMvcConfigurer" interface
2) Controller-level configuration using the @CrossOrigin annotation on individual controllers or even specific handler methods
3) Spring Boot properties/application.properties/yml which is particularly useful for simple cases or when externalized configuration is preferred

Q: What is the @Target annotation used for?
A: The @Target annotation in Java is a meta-annotation (used to annotate other annotations) and specifies the kinds of Java elements to which an annotation type can be applied. By using @Target, you can restrict the usage of an annotation, ensuring it is only used in appropriate contexts. @Target takes an argument of ElementType enumeration to specify where an annotation type is allowed to be used in the code. The possible ElementType values include: TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE, ANNOTATION_TYPE, PACKAGE, TYPE_PARAMETER, TYPE_USE. 

Q: What is the @Retention annotation used for?
A: The @Retention annotation in Java specifies the retention policy for an annotation type. The retention policy determines at what point an annotation is discarded during the compilation and execution of a program. Essentially, @Retention defines how long annotations with the annotated type are to be retained. @Retention takes a single argument, an enum of type RetentionPolicy, which defines the lifespan of the annotated elements. There are three RetentionPolicy values: SOURCE, CLASS, RUNTIME.

Q: What is the @Inherited annotation used for?
A: The @Inherited meta-annotation indicates that an annotation type is automatically inherited by subclasses. If an annotation is annotated with @Inherited, and if a class is annotated with this annotation, then its subclasses will also inherit the annotation implicitly. Applies only to class-level annotations. It does not affect method, field, or other types of annotations. If the subclass itself is annotated with the same type of annotation, the subclass's annotation will override the inherited one.

Q: What is the @Documented annotation used for?
A: The @Documented meta-annotation indicates that any use of an annotated annotation should be documented by javadoc and similar tools, meaning that the annotation should be included in the generated documentation for the annotated element. It does not change the annotation's runtime behavior but impacts its visibility in documentation.

Q: What is the @Configuration annotation used for?
A: The @Configuration annotation in Spring Framework is used to indicate that a class declares one or more @Bean methods and may be processed by the Spring container to generate bean definitions and service requests for those beans at runtime.

Q: What is the @EnableAutoConfiguration annotation used for?
A: The @EnableAutoConfiguration annotation enables Spring Boot to automatically configure your application based on the dependencies present in your project's classpath. This auto-configuration attempts to guess and configure beans that you are likely to need.

Q: What is the @ComponentScan annotation used for?
A: The @ComponentScan annotation in Spring Framework is used to specify the packages to scan for components. Components are classes annotated with stereotypes like @Component, @Service, @Repository, @Controller, and others that define beans to be managed by the Spring container. By default, Spring scans the classes in the same package as the configuration class where @ComponentScan is declared, but you can configure it to scan other packages.

Q: What are the different modules in Spring?
A: These are they key modules within the Spring framework:
1) Core container, including spring-core, spring-beans, spring-context and spring-expression
2) Data access/integration, including spring-jdbc, spring-tx, spring-orm, spring-oxm, spring-jms
3) Web, including spring-web, spring-webmvc, spring-websocket, spring-webflux
4) Messaging, including spring-messaging
5) Testing, including spring-test
6) AOP, including spring-aop
7) Instrumentation, including spring-instrument

Q: What does the @Value annotation do?
A: The @Value annotation in the Spring Framework is used to inject values into fields, methods, and method/constructor parameters. It serves as a convenient way to externalize configuration to properties files or other sources, allowing for more flexible and maintainable code. The annotation supports both literal values (@Value("John Doe")) and expressions (@Value("${user.name}") or SpEL  @Value("#{systemProperties['user.region']}")) that can be evaluated against the environment or other configuration sources.

Q: What are the differences between @RequestMapping and @GetMapping?
A: They are both annotations used for mapping web requests to specific handler functions within controller classes. While they both serve the purpose of handling requests, there are some key differences:
1) Versatility - @RequestMapping can handle any HTTP method unless specified otherwise while @GetMapping is a composed annotation that acts as a shortcut for @RequestMapping(method = RequestMethod.GET).
2) Simplicity - @GetMapping, unlike @RequestMapping offers a more straightforward and concise way to define handlers for GET requests, improving code readability.

Q: What is the Spring Actuator? 
A: Spring Actuator is a part of the Spring Boot framework that provides a set of ready-to-use features to help you monitor and manage your Spring Boot application. It is mainly used in the production environment to gain insights into the application and its runtime characteristics, such as health checks (including database connections, disk space, custom checks, and more), metrics and statistics (including JVM metrics, HTTP requests, cache statistics, and datasource metrics, among others), application info (exposing arbitrary application information, version numbers, custom information, and details about environment), audit events (security such as login attempts), dynamic logging, environment information, thread dumps, http traces, etc. 

Q: Is it possible to create custom actuator endpoints and how?
A: Yes, custom actuator endpoints can be created and can be useful for exposing application-specific metrics, health checks, or other data via the Actuator infrastructure. To create one, we need to ensure we have the spring-boot-starter-actuator dependency, and then we'd have to implement the @Endpoint or @WebEndpoint annotation to create an actuator endpoint. After that, we can create a method annotating it with either @ReadOperation for HTTP GET, @WriteOperation for HTTP POST, and @DeleteOperation for HTTP DELETE.

Q: What are bean scopes?
A: Bean scopes in Spring define the lifecycle and visibility of a bean within the application context. The scope of a bean determines how instances of that bean are created and shared. They can be configured with the @Scope annotation. Spring provides several built-in bean scopes:
1) Singleton (default) - Only one instance of the bean is created for the Spring container, and the same instance is returned every time the bean is requested.
2) Prototype - A new instance of the bean is created each time it is requested from the container.
3) Request - A new instance of the bean is created for each HTTP request. This scope is valid only in the context of a web-aware Spring ApplicationContext.
4) Session - A new instance of the bean is created for each HTTP session. This scope is also specific to a web-aware Spring ApplicationContext.
5) Application - A single instance of the bean is created for the lifecycle of a ServletContext. This is similar to a singleton scoped bean but scoped to the web application's lifecycle.
6) Websocket - A single instance of the bean is scoped to the lifecycle of a WebSocket. This scope is specific to applications using WebSocket connections.
Besides the built-in scopes, Spring allows for the creation of custom scopes if the provided scopes do not meet an application's specific needs.

Q: What are the cascade types in the Java Persistence API (JPA)?
A: When you perform some operations on the parent entity, you can specify that you want the same operation to be automatically applied to the associated child entities. This feature is particularly useful for managing the state of related entities and maintaining referential integrity in the database. JPA supports several types of cascading operations:
1) CascadeType.PERSIST - When persisting an entity, also persist the entities held in its fields.
2) CascadeType.MERGE - When merging the state of an entity, also merge the state of the entities held in its fields.
3) CascadeType.REMOVE - When deleting an entity, also delete the entities held in its fields
4) CascadeType.DETACH - When an entity is detached from the persistence context, also detach the entities held in its fields.
5) CascadeType.REFRESH - When refreshing the state of an entity from the database, also refresh the state of the entities held in its fields.
6) CascadeType.ALL - Applies all the above cascade operations to the associated entities

Q: What are Spring Boot starters?
A: Spring Boot Starters are a set of convenient dependency descriptors that you can include in your application's build configuration. Each starter is designed to simplify the Maven or Gradle build configuration by providing a curated list of dependencies needed for a specific type of development.

Q: How are transactions implemented in Spring?
A: Transactions are managed through the Spring Transaction Management abstraction, which provides a consistent programming model across different transaction APIs like JDBC, JPA, Hibernate, and JTA. This abstraction allows developers to manage transactions declaratively through annotations or programmatically using the PlatformTransactionManager/TransactionTemplate. To use annotation-driven transaction management we need to enable it with the @EnableTransactionManagement in the application configuration and then annotate classes or methods with @Transactional. 

Q: What are the types of transaction propagation in Spring?
A: Transaction propagation in Spring Framework defines how transactions relate to each other in terms of boundaries and behaviors. Spring offers several types of transaction propagation:
1) REQUIRED (default) - If there is an existing transaction, the current method will run within that transaction. If no transaction exists, Spring will start a new one.
2) REQUIRES_NEW - Spring suspends the current transaction if it exists and starts a new one. After completion, it resumes the previous transaction.
3) SUPPORTS - If there is an existing transaction, the method will run within this transaction. If there is no existing transaction, the method will run non-transactionally.
4) NOT_SUPPORTED - If there is an existing transaction, Spring suspends it, and the method runs without transactional context. The original transaction is resumed afterward.
5) MANDATORY - Requires an existing transaction. If there's no active transaction, Spring throws an exception.
6) NEVER - The method should never run in a transactional context. If there's an existing transaction, Spring throws an exception.
7) NESTED - If an existing transaction is present, the method will be executed in a nested transaction, which is a transaction within another transaction, having its own commit and rollback settings. If no existing transaction is present, it behaves like REQUIRED.

Q: How does the @Transactional annotation work internally?
A: When the Spring ApplicationContext starts, it scans for beans annotated with @Transactional or configured through XML to be transactional. For these beans, Spring creates a proxy that wraps the original bean. This proxy is responsible for managing the transactional behavior. When a method annotated with @Transactional is called, the call goes through the proxy. The proxy intercepts the method call and determines whether to start a new transaction or join an existing one based on the method's @Transactional attributes and the current transaction context. Spring abstracts the underlying transaction management mechanism through the PlatformTransactionManager interface. The specific implementation manages the actual transaction lifecycle, including creation, commit, and rollback of transactions, according to the configured transaction attributes (like propagation, isolation level, timeout, etc.). Given this information, the workflow is as follows:
1) A method annotated with @Transactional is invoked.
2) The proxy surrounding the bean intercepts the method call.
3) The proxy consults the PlatformTransactionManager to apply the transaction attributes (start a new transaction, join an existing one, etc.).
4) The method logic is executed.
5) If the method completes successfully, the transaction is committed; if an exception is thrown, the transaction is checked against the rollback rules to determine if it should be rolled back.

Q: What is Spring Data JPA, and how does it simplify data access in Spring applications?
A: Spring Data JPA is part of the larger Spring Data project that aims to simplify data access by providing a set of repository abstractions. It allows developers to easily implement JPA-based repositories without having to write boilerplate code. The benefits can be listed as such:
1) Provides a common repository interface with basic CRUD operations.
2) Allows the definition of query methods directly in the repository interface using method naming conventions.
3) Supports JPQL, native SQL queries, and Criteria API for complex queries.
4) Built-in support for auditing changes to entities.

Q: What are some common issues with using @Autowired, and how can they be resolved?
A: There are several most common issues that can arise when using @Autorwired:
1) Circular dependencies can occur when two or more beans depend on each other. This can be resolved by using @Lazy to defer injection or refactor the design to break the dependency cycle.
2) No qualifying bean can occur when Spring cannot find a bean to inject. To resolve this, we need to ensure the bean is defined in the context or to use @Qualifier to specify the bean

Q: What is the difference between @RequestBody and @ResponseBody annotations?
A: @RequestBody maps the HTTP request body to a Java object. It is used in method parameters to receive data in JSON or XML format. @ResponseBody maps the Java method return value to the HTTP response body. Used in method return types to send data in JSON or XML format.

Q: How do you handle exceptions in Spring MVC?
A: @ExceptionHandler can be used to handle exceptions in the same controller. @ControllerAdvice can be used to handle exceptions globally across multiple controllers. Extending ResponseEntityExceptionHandler class can be used for handling standard Spring MVC exceptions.

Q: What are some best practices for designing RESTful APIs with Spring Boot?
A: The most important ones would have to be the following:
1) Versioning: Use URI versioning, query parameters, or custom headers to version your APIs.
2) Error Handling: Provide meaningful error responses using standard HTTP status codes and messages.
3) Pagination: Use query parameters for pagination.
4) Security: Implement authentication and authorization using Spring Security.

Q: What are the differences between @RestController and @Controller?
A: @RestController combines @Controller and @ResponseBody, eliminating the need to annotate each method with @ResponseBody. It is used for RESTful web services that return data (JSON/XML). @Controller is used for web controllers/MVC applications that return views (HTML/JSP).
