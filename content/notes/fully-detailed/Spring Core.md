## 1. Inversion of control (IoC) and Dependency Inversion (DI)
### 1.1. IoC: definition, benefits, explanation 
- ==IoC== is a design principle in which control over object creation and management is transferred from the application code to a container, in Spring specifically, the Spring IoC container.
- The benefits of this approach are:
	1. Decoupled object creation from business logic;
	2. Enhanced testability and maintainability;

### 1.2. IoC container in Spring: BeanFactory and ApplicationContext
1. `BeanFactory` is the most basic IoC container in Spring, defined by the `org.springframework.beans.factory.BeanFactory` interface in the `spring-beans` module. 
	- Internally, Spring always uses `BeanFactory` as the core implementation to manage beans.
	- Even when you use `ApplicationContext`, it delegates bean instantiation to a `BeanFactory`.
	- The default implementation, `DefaultListableBeanFactory`, is used under the hood in Spring Boot applications.

	- The characteristics of the `BeanFactory`: 
		- **Lazy initialization**: Beans are created only when they are first requested (lazy loading).
		- **Basic dependency injection**: Supports constructor-based and setter-based dependency injection.
		- **No built-in event propagation**: Unlike `ApplicationContext`, it does not support events and listeners.
		- **Manual resource loading**: Requires explicit resource handling to read configurations (e.g., XML files).

2. `ApplicationContext` is a more advanced container that extends `BeanFactory` and provides additional features useful for **enterprise applications**.
-  When you run a Spring Boot app (`@SpringBootApplication`), it **automatically creates an `ApplicationContext`**.
- Uses `AnnotationConfigApplicationContext` or `WebApplicationContext`, depending on the environment.
    - `WebApplicationContext` is automatically instantiated when a Spring Boot web app starts and it is used to manage web-related beans like Controllers, Interceptors, and View Resolvers.
    - Spring Batch & Spring Integration also uses `ApplicationContext` for job definitions, message processing, and scheduling.

    - Key Features of ApplicationContext: 
		- **Eager initialization**: Beans are created at startup unless explicitly marked as lazy (`@Lazy`).
		- **Event propagation**: Supports Spring's event mechanism (e.g., `ApplicationListener`).
		- **Declarative mechanisms**: Supports declarative bean creation with annotations like `@Component`, `@Service`, `@Repository`.
		- **Internationalization support**: Built-in support for message sources and localization.
		- **Integration with Spring AOP**: Seamlessly supports Aspect-Oriented Programming.
		- **Automatic resource loading**: Can read configurations from XML, Java Config, or properties files.
		- **More user-friendly API**: Comes with a set of utilities for accessing application properties and resources.

### 1.3. DI: Definition, types, and examples
- ==Dependency injection== is a programming technique in which an object or function receives other objects or functions that it requires, as opposed to creating them internally. Dependency injection aims to separate the concerns of constructing objects and using them, leading to loosely coupled programs.

- There are 3 main types of dependency injection used in Spring:
	1. **Constructor injection**: dependencies provided via constructor
			`public class UserService {`
			    `private final UserRepository userRepository;`
			    `public UserService(UserRepository userRepository) {`
			        `this.userRepository = userRepository;`
			    `}`
			`}`
	2.  **Setter Injection:** Dependencies are set via setter methods:
			`public class UserService {`
			    `private UserRepository userRepository;`
			    `public void setUserRepository(UserRepository userRepository) {`
			        `this.userRepository = userRepository;`
			    `}`
			`}`
	3. **Field Injection:** Dependencies are injected directly into fields (*not a recommended practice due to immutability concerns*).
			`@Autowired`
			`private UserRepository userRepository;`

## 2. AOP
### 2.1. Definition and examples
- ==Aspect-Oriented Programming (AOP)== is a programming paradigm that allows you to separate cross-cutting concerns (like logging, security, and transaction management) from the main business logic of an application. It enhances modularity by enabling the clean separation of concerns, reducing code duplication and increasing maintainability. *The phrase **"cross-cutting concerns"** refers to functionality that is needed in **many different parts of an application** but is **not specific to any one part**. Think of it like **a common rule or action** that applies everywhere. Instead of writing the same code in multiple places, you can **handle it in one central place**.*
- Examples where AOP is often used (and useful): 
	1. Logging → You want to log every method call in your application.
	2. Security → You want to check if a user is authorized before executing certain methods.
	3. Transaction Management → You want to make sure a database transaction starts before a method runs and commits after it finishes.

### 2.2. Core concepts of AOP: aspect, advice, types of advice, join point, pointcut, weaving
#### 2.2.1. Aspect
- ==An aspect== is a modular unit that encapsulates a cross-cutting concern. Instead of scattering logging, security, or transaction management logic across multiple classes, an aspect centralizes this behavior in a single place.
	- Example: Logging is a common aspect because it is needed in multiple places (e.g., method entry, exit, exception handling).
	- In Spring AOP, aspects are typically implemented using classes annotated with `@Aspect`.

#### 2.2.2. Advice
- ==Advice== represents the action taken by an aspect at a specific point in the program execution (i.e., a **join point**). It defines what should be done and when.
- Spring AOP supports several types of advice:
	1. **Before**: Executed before the target method runs.
			`@Aspect`
			`@Component`
			`public class LoggingAspect {`
			    `@Before("execution(* com.example.service.*.*(..))")`
			    `public void logBefore(JoinPoint joinPoint) {`
			        `System.out.println("Executing: " + joinPoint.getSignature());`
			    `}`
			`}`

	2. **After**: Executed after the method completes, regardless of whether it completed successfully or threw an exception.
			`@After("execution(* com.example.service.*.*(..))")`
			`public void logAfter(JoinPoint joinPoint) {`
			    `System.out.println("Completed: " + joinPoint.getSignature());`
			`}`

	3. **After Returning**: Executed only when the method successfully completes and returns a result.
			`@AfterReturning(pointcut = "execution(* com.example.service.*.*(..))", returning = "result")`
			`public void logAfterReturning(JoinPoint joinPoint, Object result) {`
			    `System.out.println("Method " + joinPoint.getSignature() + " returned: " + result);`
			`}`

	4. **After Throwing**: Executed only if the method throws an exception.
			`@AfterThrowing(pointcut = "execution(* com.example.service.*.*(..))", throwing = "ex")`
			`public void logAfterThrowing(JoinPoint joinPoint, Throwable ex) {`
			    `System.out.println("Exception in " + joinPoint.getSignature() + ": " + ex.getMessage());`
			`}`

	5. **Around**: Wraps the method execution, allowing pre-processing, post-processing, and even short-circuiting execution. *(Short-circuiting execution means stopping a method from finishing its' execution inside @Around advice based on certain conditions.)*
			`@Around("execution(* com.example.service.*.*(..))")`
			`public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {`
			    `long start = System.currentTimeMillis();`
			    `Object result = joinPoint.proceed();  // Execute the target method`
			    `long elapsedTime = System.currentTimeMillis() - start;`
			    `System.out.println("Execution time of " + joinPoint.getSignature() + ": " + elapsedTime + "ms");`
			    `return result;`
			`}`

#### 2.2.3. Join Point
- A join point is a point during the execution of the program where advice can be applied. In Spring AOP, join points are method executions.
- Examples of join points: method execution, object instantiation, exception handling, field access (not supported in Spring AOP, but possible in full-fledged AOP frameworks like AspectJ)

#### 2.2.4. Pointcut
- A pointcut is an expression that matches one or more join points, determining where advice should be applied.
- Spring AOP uses AspectJ-style expressions to define pointcuts:
	- `execution(* com.example.service.*.*(..))` - Matches all methods in the service package.
	- `execution(* com.example.service.UserService.getUser(..))` - Matches the `getUser` method in `UserService`.
	- `within(com.example.service..*)` - Matches all methods within classes in `service` package (including subpackages).
	- `@annotation(org.springframework.transaction.annotation.Transactional)` - Matches methods annotated with `@Transactional`.
- Code example:
	`@Pointcut("execution(* com.example.service.*.*(..))")`
	`public void serviceMethods() {}`
	`@Before("serviceMethods()")`
	`public void beforeServiceMethod(JoinPoint joinPoint) {`
	    `System.out.println("Before executing: " + joinPoint.getSignature());`
	`}`

#### 2.2.5. Weaving and types of weaving
- Weaving is the process of applying aspects to the target classes. It determines how and when the aspects are injected into the application.
- 3 types of weaving:
	1. **Compile-time weaving**: Aspects are woven into the bytecode at compile time (e.g., using AspectJ compiler).
	2. **Load-time weaving**: Aspects are applied during class loading using a special class loader.
	3. **Runtime weaving**: Aspects are applied dynamically at runtime (Spring AOP uses this approach via proxies).

### 2.3. Spring AOP implementation
- Spring AOP uses **dynamic proxies** for runtime weaving, creating proxy objects that intercept method calls and apply advice. Spring AOP does not modify the actual bytecode of the classes. Instead, it works by creating proxy objects that wrap around the actual objects. These proxies intercept method calls and apply aspects before delegating to the real object.
- Spring AOP automatically chooses the appropriate proxy mechanism between:
	- **JDK Dynamic Proxy**: Uses Java’s built-in proxy mechanism to create an object that implements the same interfaces as the target. Used when the target **implements at least one interface**.
	- **CGLIB Proxy**: Uses a subclass-based approach, generating a subclass of the target class at runtime. Used when the target class **does not implement an interface**.
- The Spring AOP only supports method-level join points (field access or constructor injection is not intercepted) and as it uses **proxy-based weaving** it might have minor performance overhead compared to compile-time weaving.
- In the case of a need for a more powerful AOP library, **AspectJ** can be used instead of Spring AOP as it supports compile-time weaving, field access, constructor interception, and more. 

### 2.4. AOP and Spring Transactions (@Transactional)
- When you use `@Transactional`, it **creates a proxy** around the service class to manage database transactions.
- In a class like this:
		`@Service`
		`public class PaymentService {`
			`@Transactional`
		    `public void processPayment() {`
		        `// Transaction starts (before advice)`
		        `// Execute method`
		        `// Commit or rollback transaction (after advice)`
		    `}`
		`}`
- Spring AOP ensures that:
	1. A **transaction starts before** `processPayment()` executes.
	2. If everything is successful, the **transaction is committed**.
	3. If an exception occurs, the **transaction is rolled back**.

### 2.5. AOP and the self-invocation problem
- Spring AOP **only works when the proxy is involved**. If a method in the same class calls another AOP-advised method (inside that same class), the proxy is bypassed, and the advice is **not applied**.
- The solution to this problem is to use **AOP-aware beans** by injecting the class into itself.

### 2.6. Debugging common AOP issues
- Enable AOP debugging logs in application.properties: `logging.level.org.springframework.aop=DEBUG`
- Check if your classes are managed by Spring as AOP only applies to Spring Beans. If you use new ClassName(), AOP won’t work.
- Ensure the pointcut expression is correct: Test with a simple `execution(* *(..))` to check if the pointcut matches anything.
- If a method calls another method within the same class, the proxy won’t intercept it.

## 3. Bean lifecycle
- The beans managed by the Spring IoC container have a structured lifecycle for creation, configuration, and destruction as needed.
- The lifecycle consists of several distinct phases:
	1. **Instantiation** - the Spring container creates an instance of the bean. This can be done using a default (no-args) constructor or a static/non-static factory method;

	2. **Populate Properties** - after instantiating the bean, Spring injects the required dependencies by using one of the types of dependency injection (DI);

	3. **Awareness Interfaces** (BeanNameAware, BeanFactoryAware, ApplicationContextAware) - Spring provides several "Aware" interfaces that allow beans to access specific framework objects:
		- BeanNameAware: The `setBeanName(String name)` method provides the bean's name;
		- BeanFactoryAware: The `setBeanFactory(BeanFactory beanFactory)` method provides access to `BeanFactory` to programmatically get other beans;
		- ApplicationContextAware: The `setApplicationContext(ApplicationContext applicationContext)` method provides access to `ApplicationContext`;

	4. **Pre-Initialization** (BeanPostProcessor) - Before initialization, `BeanPostProcessor` comes into play. This allows us to modify beans before their initialization logic executes. The method `postProcessBeforeInitialization()` runs before any custom initialization logic.

	5. **Initialization** (Custom init methods and InitializingBean) - At this stage, the bean executes its initialization logic. There are three ways to define this:
		1. Using `@PostConstruct` Annotation (recommended approach as it doesn't rely on Spring-specific classes, making the class more decoupled and easy to read);
		2. Implementing `InitializingBean` and overriding `afterPropertiesSet()`;
		3. Specifying an init method in XML or Java Configuration;

	6. **Post-Initialization** (BeanPostProcessor) - Similar to pre-initialization, a `BeanPostProcessor` is a special Spring component that allows **modification of beans before and after initialization**.. The method `postProcessBeforeInitialization()` runs before any custom initialization logic, while `postProcessAfterInitialization()` runs after any custom initialization logic.
		- Example of a custom BeanPostProcessor:
			`@Component` 
			`public class MyBeanPostProcessor implements BeanPostProcessor {` 
				`@Override` 
				`public Object postProcessBeforeInitialization(Object bean, String beanName) {`
				 `System.out.println("Before Initialization: " + beanName);` 
				 `return bean;` 
				`}`
				 
				`@Override 
			`public Object postProcessAfterInitialization(Object bean, String beanName)` { 
				 `System.out.println("After Initialization: " + beanName);` 
				 `return bean;` 
			 `}` 
		 `}`

	8. **Ready to Use** - at this point, the bean is fully initialized and can be used in the application.

	9. **Destruction** - Before a bean is removed from the container, Spring provides hooks to perform cleanup.
		- Using `@PreDestroy` Annotation (recommended approach as it doesn't rely on Spring-specific classes, making the class more decoupled and easy to read)
		- Implementing `DisposableBean`
		- Specifying a destroy method in Java or XML Configuration

## 4. Spring annotations
### 4.1. Component scanning & stereotype
- ***These annotations help Spring detect and manage beans automatically.***
- **@Component**: Marks a class as a Spring-managed bean, making it eligible for dependency injection. Spring automatically detects it if component scanning is enabled (@ComponentScan).
- **@Service**: A specialization of @Component meant for the service layer. Provides additional metadata, aiding in clarity in the architecture.
- **@Repository**: A specialization of @Component for data access objects (DAOs). Also enables Spring’s exception translation mechanism, which converts JDBC exceptions into Spring’s `DataAccessException`.
- **@Controller**: Specialized @Component for handling web requests in Spring MVC. Used in combination with @RequestMapping to define endpoints.
- **@RestController** (extension of @Controller): Combination of @Controller and @ResponseBody, meaning all methods return data directly instead of resolving a view.

### 4.2. Dependency Injection Annotations
- ***These annotations help with managing dependencies between beans.***
- **@Autowired**: Automatically injects dependencies into a Spring-managed bean. Can be applied to constructors, setters, or fields. By default, Spring will fail if no suitable bean is found unless marked with `@Autowired(required = false)`.
- **@Qualifier**: Used when multiple beans of the same type exist, helping to specify which one to inject.
- **@Primary**: Marks a bean as the default candidate when multiple beans of the same type exist. If @Primary and @Qualifier conflict, @Qualifier takes precedence. If multiple beans are marked @Primary, Spring will still throw `NoUniqueBeanDefinitionException`. Does not help if `@Autowired(required = false)` is used, as it still requires an explicit candidate.
- **@Value**: Injects values from properties files or environment variables.

### 4.3. Bean Scopes & Lifecycle
- ***Spring provides different bean scopes and lifecycle hooks.***
- **@PostConstruct / @PreDestroy**: Lifecycle callbacks executed before and after the bean is initialized/destroyed.
- **@Scope**: Defines the scope of a Spring bean which determines the lifespan and visibility of a bean within the application context. By default, Spring beans are singleton, but other scopes are available depending on the use case:
	- Singleton: default scope, Ensures that only one instance of the bean is created per Spring container. The same instance is returned whenever the bean is requested. Used for stateless beans that share a common state across the application. Since the same instance is shared across threads, avoid using mutable state in singleton beans in multi-threaded environments.
	- Prototype: A new instance of the bean is created every time it is requested from the container. The Spring container does not manage the full lifecycle of prototype beans (e.g., it does not invoke @PreDestroy). Used when a bean has instance-specific state and/or for short-lived beans that should not be shared across multiple components. Spring does not call destruction callbacks so this can cause memory leaks if the prototype beans are not properly cleaned up.
	- Request (only for web apps): A new instance is created for each HTTP request. Once the request is complete, the bean is discarded. Should be used when a bean has to hold request-specific data, such as authentication details or request-scoped caches. If used outside a web-aware environment, it will cause an error.
	- Session (only for web apps): A new instance is created per HTTP session. The bean is shared within a session but not across multiple sessions. It can be used for storing user-specific data for the duration of a session (e.g., shopping cart information). Beans stored in session scope consume memory until the session is invalidated so they should be carefully managed to avoid potential issues.
	- Application (only for web apps): A single instance is created for the entire web application and is shared across all users and requests. Similar to singleton, but specifically tied to the ServletContext. Useful for maintaining global application-level state (e.g., system configuration settings). Can cause memory issues if it holds large amounts of data.
	- Websocket (only for WebSocket apps): A new instance is created for each WebSocket session and destroyed when the session is closed. Used in real-time applications when maintaining state for a connected WebSocket session (e.g., live chat applications).