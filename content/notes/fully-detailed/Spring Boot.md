## 1. Auto-configuration in Spring Boot
### 1.1. Definition and underlying mechanisms
- ==Auto-Configuration== in Spring Boot is a mechanism that automatically configures the Spring application context based on the classpath dependencies and application settings. It helps reduce boilerplate code and simplifies configuration by detecting available beans and enabling features accordingly, eliminating the need for extensive XML configurations or manual bean definitions, and providing a convention-over-configuration approach. This ensures that sensible defaults are applied while allowing customization as needed.

- Spring Boot Auto-Configuration works using the following key mechanisms:
	1. @SpringBootApplication (or @EnableAutoConfiguration) has to be present to implicitly enable the auto-configuration
	2. The annotation internally calls the `AutoConfigurationImportSelector` which uses `SpringFactoriesLoader` to scan the classpath for `META-INF/spring.factories` files in all JARs available in the classpath, and loads the fully qualified class names of various components such as auto-configuration classes, application listeners, and other Spring integrations (Spring data repos, cache implementations, etc.)
	3. Conditional checks are performed after that (@ConditionalOnClass, @ConditionalOnBean, etc.) to determine which auto-configurations should be applied.
	4. If no manual bean definition exists, Spring Boot registers auto-configured beans.

### 1.2. Conditional beans using annotations
- Auto-Configuration beans use conditional annotations to determine if they should be registered:
	- **@ConditionalOnClass**: Checks if a class is present on the classpath.
	- **@ConditionalOnMissingClass**: Checks if a class is not present.
	- **@ConditionalOnBean**: Checks if a specific bean exists in the context.
	- **@ConditionalOnMissingBean**: Registers a bean only if it doesn’t already exist.
	- **@ConditionalOnProperty**: Enables a bean based on application properties.
	- **@ConditionalOnExpression**: Uses a SpEL expression to determine conditions.
	- **@ConditionalOnWebApplication**: Ensures the app is a web application.

- Example of conditional auto-configuration for a `DataSource`. If no `DataSource` bean is present, this configuration class will register one with the specified properties:
	`@Configuration`
	`@ConditionalOnClass(DataSource.class)`
	`@EnableConfigurationProperties(DataSourceProperties.class)`
	`public class DataSourceAutoConfiguration {`
    
	    @Bean
	    @ConditionalOnMissingBean
	    public DataSource dataSource(DataSourceProperties properties) {
	        return DataSourceBuilder.create()
	            .url(properties.getUrl())
	            .username(properties.getUsername())
	            .password(properties.getPassword())
	            .build();
	    }
	`}`

### 1.3. Key annotations for auto-config
- **@EnableAutoConfiguration**: Enables auto-configuration in a Spring Boot application. Typically used implicitly via @SpringBootApplication.
- **@SpringBootApplication**: A combination of @EnableAutoConfiguration, @ComponentScan, and @Configuration. Commonly used as the entry point of a Spring Boot application.
- **@AutoConfigureOrder**: Specifies the order in which auto-configuration classes should be applied. Lower values have higher priority. Example
	- `@AutoConfigureOrder(Ordered.HIGHEST_PRECEDENCE)`
- **@AutoConfigureBefore** & **@AutoConfigureAfte**r: Used to control the execution order of auto-configuration classes. Example:
	- `@AutoConfigureBefore(HibernateJpaAutoConfiguration.class)`
	- `@AutoConfigureAfter(DataSourceAutoConfiguration.class)`
- **@ConfigurationProperties**: Binds external properties (from `application.properties/yml`) to Java objects. Used in combination with @EnableConfigurationProperties.

### 1.4. Customizing auto-configuration
- Spring Boot provides several ways to customize auto-configuration behavior:
	1. Many auto-configurations expose properties that can be overridden by using `application.properties` or `application.yml`
	2. Using `spring.autoconfigure.exclude` can disable specific auto-configuration classes. Example: `spring.autoconfigure.exclude=org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration`
	3. Using @ImportAutoConfiguration can selectively enable auto-configuration for specific classes.


## 2. Spring Boot starters
- ==Spring Boot starters== are predefined dependency bundles that include necessary libraries for specific functionalities. They reduce the need to manage dependencies manually.
- Some of the most commonly used starters include:
	- spring-boot-starter-web → Spring MVC, embedded Tomcat, Jackson (JSON processing).
	- spring-boot-starter-data-jpa → Spring Data JPA, Hibernate.
	- spring-boot-starter-security → Spring Security integration.
	- spring-boot-starter-test → Testing libraries like JUnit, Mockito, and AssertJ.
- It is also possible to create custom Spring Boot starters by following this procedure:
	1. Create a separate module with dependencies and auto-configuration.
	2. Define a spring.factories file for auto-configuration loading.
	3. Publish as a Maven/Gradle dependency.


## 3. Embedded servers in Spring Boot (Tomcat, Jetty, Undertow)
### 3.1. Definition and defaults in Spring
- ==Embedded servers== allow Spring Boot applications to run as standalone services without requiring an external application server. This means that instead of deploying a .war file to a separate Tomcat or Jetty instance, the application comes with an embedded web server and runs as an executable JAR.
- There are many advantages to this approach such as:
	- Simplifies Deployment: No need for external Tomcat/Jetty installation.
	- Self-Contained Applications: Runs independently as a JAR file.
	- DevOps-Friendly: Easier containerization (e.g., Docker).
	- Microservices-Ready: Ideal for deploying lightweight services.
- The default Spring Boot embedded server is Apache Tomcat which comes with the `spring-boot-starter-web` dependency package.

### 3.2. Embedded Tomcat configuration
- There is a multitude of configuration properties exposed for the embedded Tomcat server which can be managed through `application.properties` or `application.yml`: 
	- Changing the default port (default is 8080): `server.port=8081`
	- Setting maximum thread count (how many requests Tomcat can handle concurrently): `server.tomcat.max-threads=200`
	- Enable HTTP/2 for improved performance: `server.http2.enabled=true`
	- Customize the connection timeout (default is 30s): `server.tomcat.connection-timeout=10s`
	- Enable compression for reduced response size and faster transmissions: `server.compression.enabled=true`
		`server.compression.mime-types=text/html,text/xml,text/plain,text/css,application/javascript,application/json 
		`server.compression.min-response-size=1024`
	- Enforce HTTPS (redirect all traffic from HTTP to HTTPS): 
		`server.ssl.enabled=true`
		`server.ssl.key-store=classpath:keystore.jks`
		`server.ssl.key-store-password=secret`
		`server.ssl.key-alias=myalias`
		`server.port=8443`

### 3.3. Switching between embedded servers and embedded server characteristics
- Spring Boot allows to switch the embedded server by excluding Tomcat and adding another server like Jetty or Undertow.
- First, we need to exclude Tomcat from the dependencies:
	`<dependency>`
	    `<groupId>org.springframework.boot</groupId>`
	    `<artifactId>spring-boot-starter-web</artifactId>`
	    `<exclusions>`
	        `<exclusion>`
	            `<groupId>org.springframework.boot</groupId>`
	            `<artifactId>spring-boot-starter-tomcat</artifactId>`
	        `</exclusion>`
	    `</exclusions>`
	`</dependency>`
- Then, the new embedded server can be added as a standard dependency, for example, Jetty:
	`<dependency>`
	    `<groupId>org.springframework.boot</groupId>`
	    `<artifactId>spring-boot-starter-jetty</artifactId>`
	`</dependency>`

- Main characteristics between Tomcat, Jetty and Undertow:

| Feature               | Tomcat          | Jetty         | Undertow              |
| --------------------- | --------------- | ------------- | --------------------- |
| Performance           | Moderate        | Fast          | Very fast             |
| Memory usage          | Moderate        | Low           | Low                   |
| HTTP/2 support        | Yes             | Yes           | Yes                   |
| Asynchronous requests | No              | Yes           | Yes                   |
| Primary use case      | General purpose | Microservices | High-performance APIs |

### 3.4. Deploying a WAR to an external Tomcat server 
- To deploy an application to an external Tomcat instance instead of using an embedded server, the app must first be packaged as a WAR file. The flow is the following:
	1. Modify SpringBootApplication class to extend SpringBootServletInitializer:
		`@SpringBootApplication`
		`public class MyApplication extends SpringBootServletInitializer {`

	    `@Override`
	    `protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {`
	        `return builder.sources(MyApplication.class);`
	    `}`
	`}`
	2. Change the packaging type to WAR in the `pom.xml`: `<packaging>war</packaging>`
	3. Exclude embedded Tomcat from the dependencies since Tomcat is already provided by the external server
	4. Build the WAR file by using `mvn clean package` and then deploy to Tomcat by copying the `war` file to Tomcat's `webapps` directory. The Tomcat server can then be started by using `bin/startup.sh` or `bin/startup.bat`. 


## 4. Spring Boot actuator
### 4.1. Definition
- ==Spring Boot Actuator== is a production-ready feature in Spring Boot that provides monitoring and management capabilities for applications. It helps in exposing runtime insights about the application, including health status, environment properties, system metrics, and application details via HTTP, JMX, or logs.
- It provides built-in monitoring and management endpoints, supports exposing metrics, health checks, environment details, and logs.
- It supports customizable security to restrict access to sensitive endpoints and enables dynamic configuration updates without restarting the application.
- The actuator can also be integrated with Prometheus, Grafana, Zipkin, and other monitoring tools.

### 4.2. Configuration
- To enable it, the respective dependency has to be added (`spring-boot-starter-actuator`)
- By default, only a few endpoints are exposed. If any other ones are required, they need to be explicitly exposed: `management.endpoints.web.exposure.include=health,info,metrics` or to expose all of them: `management.endpoints.web.exposure.include=*`
- The actuator endpoints can be accessed via /actuator/{endpoint}. The common endpoints are as follows:
	- `/actuator/health` - Shows application health (UP/DOWN) based on system and custom checks.
	- `/actuator/info` - Displays general application info (e.g., version, name).
	- `/actuator/metrics` - Provides various performance-related metrics.
	- `/actuator/env` - Shows environment properties (system properties, application properties).
	- `/actuator/beans` - Lists all Spring beans in the application context.
	- `/actuator/mappings` - Displays all URL mappings in the application.
	- `/actuator/threaddump` - Shows a thread dump of the running application.
	- `/actuator/httptrace` - Provides details on last 100 HTTP requests.
	- `/actuator/loggers` - Displays and configures logging levels dynamically.

### 4.3. Customizing pre-existing actuator endpoints and creating custom actuator endpoints
- There exists the functionality to customize the existing actuator endpoints or to create custom endpoints which expose additional application-specific metrics.
- For example, to add custom info in `/actuator/info` the following can be added to `application.properties`:
	 `info.app.name=English Learning App`
	 `info.app.version=1.0.0`
	 `info.app.description=An interactive English language learning platform.`
	This information will then be present in the output of the `/actuator/info`  endpoint.

- To implement a custom endpoint the following flow has to be completed:
	1. Implement the following class to create an endpoint at `/actuator/customEndpoint`:
	`@Component` 
	`@Endpoint(id = "customEndpoint") 
	`public class CustomActuatorEndpoint { 
	
		@ReadOperation public Map<String, String> customData() { 
		return Map.of("message", "This is a custom Actuator endpoint!"); 
		} 
	`}`
	2. Modify the `application.properties` to expose the endpoint:
	`management.endpoints.web.exposure.include=customEndpoint`

### 4.4. Actuator with log monitoring and actuator in microservices
- The actuator also provides the capability to dynamically change the log levels via the `/actuator/loggers` endpoint. The following request would modify the logging level at runtime without restarting the app:
	`POST /actuator/loggers/org.springframework`
	`{`
  `"configuredLevel": "DEBUG"`
	`}`

- In a microservices environment, the Spring Boot actuator plays a critical role in managing critical parts of the application, such as:
	- Service discovery (via `/actuator/info` and `/actuator/health`).
	- Monitoring and tracing requests across services.
	- Dynamic logging for debugging production issues
	- Centralized metric collection with Prometheus/Grafana

## 5. Externalized configuration: definition and order of precedence
- Externalized configuration allows developers to define application settings outside the compiled code, making applications more flexible and easier to manage across different environments. Instead of hardcoding configuration values, Spring Boot enables injecting values from multiple sources such as properties files, environment variables, command-line arguments, and more.
- The following lists the different possible sources and their order of precedence (from highest to lowest priority):
	1. **Command-line arguments** (`--server.port=9090`)
	2. **Java System properties** (`-Dserver.port=9090`)
	3. **Environment variables** (`SPRING_APPLICATION_JSON`, `export SERVER_PORT=9090`)
	4. **Spring `application.properties` or `application.yml`** (inside `src/main/resources`)
	5. **@PropertySource annotations** (defined in `@Configuration` classes)
	6. **Default properties** (set via `SpringApplication.setDefaultProperties`)