## 1. Overview
### 1.1. Architecture
- ==Spring MVC== is based on the Front Controller design pattern, where a single entry point (`DispatcherServlet`) manages all HTTP requests and delegates them to appropriate components.

- The key workflow in Spring MVC is as follows:
	1. A client send an HTTP request (e.g., `GET /users`)
	2. The Front Controller (`DispatcherServlet`) acts as the central hub for handling requests. It delegates the requests to the appropriate handlers using `HandlerMapping`.
	3. `HandlerMapping` then maps the request to the correct `@Controller` method based on URL patterns and/or annotations.
	4. The controller processes the request, interacts with the necessary services, and prepares a `Model` object.
	5. The `ViewResolver` determines the appropriate view (e.g., JSP, Thymeleaf, JSON response).
	6. The selected view is then rendered and returned as an HTTP response.

- The key components in the processing of each request are the following:
	1. Front controller (`DispatcherServlet`) - the heart of Spring MVC which routes requests to controllers. Configured via web.xml or Java-based configuration.
	2. Controller layer - Controllers handle client requests and return appropriate responses. Defined using the `@Controller` or `@RestController` annotation. Methods are mapped to request URLs using `@RequestMapping`.
	3. Model (Data layer) - Represents business data and logic. Typically consists of POJOs (Plain Old Java Objects), stored as attributes in `Model` and used in views.
	4. View (Presentation Layer) - Displays data to the user. Some common view technologies are: JSP, Thymeleaf, JSON, XML.

### 1.2. Key components
1. `HandlerMapping` - Determines which controller should handle a request. Common implementations: 
	- `RequestMappingHandlerMapping`: maps `@RequestMapping` annotations to methods;
	- `SimpleUrlHandlerMapping`: maps URLs to handler beans.

2. `ViewResolver` - Determines how views are resolved. Common implementations: 
	- `InternalResourceViewResolver` uses JSP files.
	- `ThymeleafViewResolver` resolves Thymeleaf templates.
	- `MappingJackson2JsonView` converts model data to JSON.

3. **Data Binding** - Uses `@ModelAttribute` and `@RequestParam` for parameter binding. Example:
	`@PostMapping("/users")`
	`public String createUser(@ModelAttribute User user) {`
	    `userService.saveUser(user);`
	    `return "redirect:/users";`
	`}`

4. **Form handling** - Spring MVC supports form binding with `@ModelAttribute`. Example HTML form and controller:
	`<form action="/users" method="post">`
	    `<input type="text" name="name">`
	    `<input type="email" name="email">`
	    `<button type="submit">Submit</button>`
	`</form>`
	
	`@PostMapping("/users")`
	`public String submitUserForm(@ModelAttribute User user) {`
	    `userService.saveUser(user);`
	    `return "success";`
	`}`

5. **Exception Handling** - done with `@ControllerAdvice`. Example:
	`@ControllerAdvice`
	`public class GlobalExceptionHandler {`

	    `@ExceptionHandler(UserNotFoundException.class)`
	    `public ResponseEntity<String> handleUserNotFound(UserNotFoundException ex) {`
	        `return ResponseEntity.status(HttpStatus.NOT_FOUND).body(ex.getMessage());`
	    `}`
	`}`

6. Interceptors - allows pre-processing and post-processing of requests. Example with config:
	`public class LoggingInterceptor implements HandlerInterceptor {`

	    @Override
	    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
	        System.out.println("Incoming request: " + request.getRequestURI());
	        return true;
	    }
	`}`
	
	`@Configuration`
	`public class WebConfig implements WebMvcConfigurer {`
	    `@Override`
	    `public void addInterceptors(InterceptorRegistry registry) {`
	        `registry.addInterceptor(new LoggingInterceptor());`
	    `}`
	`}`

7. **CORS** - allow requests from different origins. Example:
	`@CrossOrigin(origins = "http://example.com")`
	`@RestController`
	`@RequestMapping("/api/users")`
	`public class UserRestController {`
	    `// Methods`
	`}`

8. **File upload handling** - supports file uploads with `MultipartFile`. Example:
	`@PostMapping("/upload")`
	`public String uploadFile(@RequestParam("file") MultipartFile file) {`
	    `fileService.store(file);`
	    `return "File uploaded successfully";`
	`}`


## 2. REST Controllers
### 2.1. @RestController - definition
- ==The @RestController annotation== in Spring Boot is a specialized version of @Controller that simplifies the development of RESTful web services. It combines @Controller and @ResponseBody, ensuring that all methods return data directly rather than resolving to a view.
- The annotation is useful for:
	1. Exposes RESTful web services by handling HTTP requests.
	2. Returns JSON/XML responses, making it ideal for APIs consumed by web or mobile clients.
	3. Reduces boilerplate by eliminating the need for explicit @ResponseBody in each method.

### 2.2. RESTful design principles
1. APIs should expose **resources** rather than actions. Example: 
	- `/api/products` (Good, represents a resource)
	- `/api/getProducts` (Bad, action-based)
2. **HTTP methods and status codes**:

| Method | Usage                       | Example endpoint     | Success Status           |
| ------ | --------------------------- | -------------------- | ------------------------ |
| GET    | Retrieve resources          | `/api/products`      | 200 OK                   |
| POST   | Create a new resource       | `/api/products`      | 201 Created              |
| PUT    | Update an existing resource | `/api/products/{id}` | 200 OK or 204 No content |
| DELETE | Remove a resource           | `/api/products/{id}` | 204 No content           |

3. **Stateless Communication**: REST APIs should be stateless, meaning each request must contain all necessary information (e.g., authentication token). Avoid storing session state on the server.
4. **Content Negotiation** - Spring automatically converts Java objects to JSON (or XML) based on the Accept header. Uses message converters like MappingJackson2HttpMessageConverter for JSON.

### 2.3. HATEOAS (Hypermedia as the Engine of Application State)
- ==HATEOAS== is a principle of RESTful API design where responses include hypermedia links that allow clients to dynamically navigate the API without needing hardcoded endpoint URLs. This improves API discoverability and reduces client-side coupling to backend changes. Spring Boot provides Spring HATEOAS, a library that makes implementing hypermedia links easier.
- There are several advantages to using HATEOAS:
	1. Decouples clients from API structure changes – Clients can navigate based on provided links rather than relying on hardcoded endpoints.
	2. Improves API discoverability – Clients can explore related resources dynamically.
	3. Follows REST Maturity Level 3 – As per Richardson Maturity Model, HATEOAS enables self-descriptive APIs.
- Example:
	`@RestController`
	`@RequestMapping("/api/products")`
	`public class ProductRestController {`

    `@Autowired`
    `private ProductService productService;`

    `// Get a single product with HATEOAS links`
    `@GetMapping("/{id}")`
    `public EntityModel<Product> getProduct(@PathVariable Long id) {`
        `Product product = productService.findById(id);`
        
        // Build self-link and related resources links
        EntityModel<Product> productModel = EntityModel.of(product,
        linkTo(methodOn(ProductRestController.class).getProduct(id)).withSelfRel(),
        linkTo(methodOn(ProductRestController.class).getAllProducts()).withRel("all-products"));

        return productModel;
    `}`

    `// Get all products with HATEOAS links`
    `@GetMapping`
    `public List<EntityModel<Product>> getAllProducts() {`
        `List<Product> products = productService.findAll();`
        
        return products.stream()
            .map(product -> EntityModel.of(product,
`linkTo(methodOn(ProductRestController.class).getProduct(product.getId())).withSelfRel()))`
            `.collect(Collectors.toList());`
    `}`
`}`

- ==Explanation:==
	1. `EntityModel<T>`: Wraps the response object and includes hypermedia links.
	2. `linkTo()`: Generates self-links dynamically based on the controller method.
	3. The self-link (`/api/products/{id}`) points to the specific product.
	4. The "all-products" link (`/api/products`) allows retrieving all products.

- ==Example response which would be returned==:
`{`
    `"id": 1,`
    `"name": "Laptop",`
    `"price": 1200.00,`
    `"_links": {`
        `"self": {`
            `"href": "http://localhost:8080/api/products/1"`
        `},`
        `"all-products": {`
            `"href": "http://localhost:8080/api/products"`
        `}`
    `}`
`}`
- HATEOAS also supports paged responses. If the API supports pagination, you can use **`PagedModel`** to add navigational links for pages. This adds pagination metadata and links like "next", "previous", and "self".

### 2.4. Testing
- Unit testing for controllers is primarily done with `MockMVC`. 
- Integration testing is supposed to be done primarily with `TestRestTemplate`. 

### 2.5. API versioning strategies
- API versioning strategies ensure backward compatibility for clients. These are the three most common approaches:
	1. URL-based versioning: `/api/v1/users`
	2. Header-based versioning: Accept: `application/vnd.company.app-v1+json`
	3. Query parameter versioning: `/users?version=1`


## 3. Request mapping
### 3.1. Definition
- ==Request Mapping== in Spring Boot is handled using mapping annotations, primarily `@RequestMapping` and its specialized variants. These annotations allows defining how HTTP requests are mapped to controller methods.
- `@RequestMapping` is the primary annotation for handling HTTP requests. It allows you to map a URL path to a specific controller or method. It can be applied at the class level (as a common path prefix) and at the method level (to specify endpoints). It can also specify URL patterns, HTTP methods, request parameters, headers, content types, etc.
- There are also specialized annotations for common HTTP methods: `@GetMapping, @PostMapping, @PutMapping, @DeleteMapping, @PatchMapping`.

### 3.2. Key attributes of @RequestMapping and related annotations
1. **URL patterns** - Defines how paths are mapped:
	- Static paths: /users
	- Dynamic segments (`@PathVariable`): `/users/{id}`
	- Wildcard (`*`) for single-level matching: `/users/*`
	- Double wildcard (`**`) for multi-level matching: `/users/**`

2. **HTTP methods**:
	- Can be specified explicitly: `@RequestMapping(value = "/users", method = RequestMethod.POST)`
	- Or with a shortcut: `@PostMapping("/users")`

3. **Request Parameters** (`@RequestParam` annotation):
	- Captures query parameters from the URL. 
	- Can have default values and be marked as required.
	- Example (for URL `GET /users/search?name=John&limit=5`):
		`@GetMapping("/search")`
		`public List<User> searchUsers(@RequestParam String name,` 
				                `@RequestParam(defaultValue = "10") int limit) {`
	    `return userService.searchByName(name, limit);`
		`}`

4. **Path variables** (`@PathVariable` annotation) - extracts values from the URL path

5. **Consuming and producing data** (`consumes` and `produces`):
	- `consumes`: Defines what request content type is accepted.
	- `produces`: Defines what response content type is returned.
	- Example:
		`@PostMapping(value = "/users", consumes = "application/json", produces = "application/json")`
		`public User createUser(@RequestBody User user) {`
		    `return userService.save(user);`
		`}`

6. **Matrix variables** (`@MatrixVariable`) - Less common, but allows passing parameters within the URL path.
	- Example:
		`@GetMapping("/users/{id}")`
		`public User getUser(@PathVariable String id, @MatrixVariable String role) {`
		    `return userService.findByRole(id, role);`
		`}`

7. **Handling multiple URL mappings** - A method can also handle multiple URL patterns
	- Example (this would handle both `/users` and `/members`):
		`@GetMapping({"/users", "/members"})`
		`public List<User> getAllUsers() {`
		    `return userService.findAll();`
		`}`


## 4. Model Binding
### 4.1. Definition
- ==Model binding== is the mechanism that allows Spring MVC to automatically map HTTP request parameters, JSON payloads, or form data to Java objects (POJOs). It simplifies handling user input by reducing the need for manual parsing and extraction.

### 4.2. Mechanisms of model binding
- Spring provides multiple ways to bind incoming request data to Java objects:
	1. `@ModelAttribute` (Form Binding) - Used in controllers to bind form data to model objects. Works with both GET and POST requests. Typically used in combination with validation.
	2. `@RequestBody` (JSON/XML Binding in REST APIs) - Binds the entire HTTP request body (e.g., JSON) to a Java object. Used mainly in @RestController classes for RESTful APIs. Requires Jackson or another JSON parser in the classpath.
	3. `@RequestParam` (Single Parameter Binding) - Used to bind individual request parameters (e.g., query parameters, form fields) to method arguments. Useful when extracting individual request parameters and/or handling filtering and search queries.
	4. `@PathVariable` (Extracting Path Variables) - Extracts values from URI paths and binds them to method parameters.
	5. `@RequestHeader` and `@CookieValue` - Bind HTTP headers and cookies to method parameters. Useful when reading authentication tokens or metadata and/or handling user session details.
- Comparison of different binding techniques:

| Feature             | @ModelAttribute                 | @RequestParam             | @PathVariable | @RequestBody      |
| ------------------- | ------------------------------- | ------------------------- | ------------- | ----------------- |
| Used for            | Form data (HTML)                | Query parameters          | URL segments  | JSON/XML payloads |
| Supports validation | Yes                             | No                        | No            | Yes               |

### 4.3. Validation
- Spring provides built-in validation support using Hibernate Validator, which is the reference implementation of the Bean Validation (JSR-303/JSR-380) specification.
- `@Valid` triggers validation based on annotations in the model.
- `BindingResult` captures validation errors and prevents binding invalid data.
- If built-in validation annotations are not sufficient, we can also define a custom validator.

### 4.4. Property editors and converters
1. Spring’s `PropertyEditor` framework helps convert request parameters into complex types. It is useful in cases of converting request parameters into complex objects like `LocalDate`, `Currency`, etc. or when applying custom formatting for specific fields.
	- Example (if a request contains `releaseDate=2024-02-10`, Spring will automatically convert it into a `LocalDate` object.):
		`@ControllerAdvice`
		`public class CustomBinderConfig {`

	    `@InitBinder`
	    `public void initBinder(WebDataBinder binder) {`
	        `binder.registerCustomEditor(LocalDate.class,   
								    `new PropertyEditorSupport() {
            `@Override`
            `public void setAsText(String text) {`
                `setValue(LocalDate.parse(text, DateTimeFormatter.ofPattern("yyyy-MM-dd")));`
            `}`
        `});`
    `}`
`}`

2. Spring’s Converter API is an alternative to `PropertyEditor` and is more flexible.
	- Example implementation:
		`@Component`
		`public class StringToCategoryConverter implements Converter<String, Category> {`
		    `@Override`
		    `public Category convert(String source) {`
		        `return Category.valueOf(source.toUpperCase());`
	    `}`
	`}`

3. Main differences between `PropertyEditor` and `Converter`:

| Feature       | `PropertyEditor`     | `Converter`                    |
| ------------- | -------------------- | ------------------------------ |
| Registration  | `@InitBinder` method | Auto-detected via `@Component` |
| Thread safety | Not thread-safe      | Thread-safe                    |
| Best for      | Form data conversion | Global conversions             |
