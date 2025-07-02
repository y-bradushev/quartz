## 1. JUnit5
### 1.1. Overview
- JUnit 5 is the latest version of the JUnit testing framework, consisting of three main sub-projects:
	- JUnit Platform: Acts as the foundation for launching JUnit 5 tests. Supports running tests from various test engines, including JUnit Jupiter and JUnit Vintage. Works with IDEs and build tools (Maven, Gradle).
	- JUnit Jupiter: Provides the new programming model and API for writing tests in JUnit 5. Introduces new annotations, assertions, and extensions. Supports parameterized tests and dynamic tests.
	- JUnit Vintage: Allows backward compatibility with JUnit 3 and JUnit 4 tests. Enables running legacy tests alongside JUnit 5 tests.

### 1.2. JUnit5 annotations
1. **Test execution annotations**:
	- `@Test` - Marks a method as a test case.
	- `@ParameterizedTest` - Allows running the same test multiple times with different parameters.
	- `@RepeatedTest(n)` - Runs a test multiple times.
	- `@Disabled` - Disables a test method or class.
	- `@Timeout` - Fails a test if it exceeds a specified time limit.
2. **Lifecycle annotations**:
	- `@BeforeEach` - Runs before each test method.
	- `@AfterEach` - Runs after each test method.
	- `@BeforeAll` - Runs once before all test methods in the class (must be static).
	- `@AfterAll` - Runs once after all test methods in the class (must be static).
3. **Display and organization**:
	- `@DisplayName("Custom name")` - Sets a custom name for a test method.
	- `@Nested` - Allows defining nested test classes for better organization.
	- `@Tag("slow")` - Categorizes tests for filtering.
4. **Parameterized test annotations**:
	- `@ValueSource` - Supplies an array of values to a parameterized test.
	- `@CsvSource` - Supplies multiple sets of parameters from inline CSV data.
	- `@CsvFileSource` - Reads parameters from a CSV file.
	- `@EnumSource` - Provides enum values as test arguments.
5. **Extensions and mocking**:
	- `@ExtendWith(MockitoExtension.class)` - Integrates Mockito with JUnit 5.

### 1.2. Assertions
- `assertEquals(expected, actual)` - Checks if two values are equal.
- `assertNotEquals(unexpected, actual)` - Checks if two values are not equal.
- `assertTrue(condition)` - Checks if the condition is true.
- `assertFalse(condition)` - Checks if the condition is false.
- `assertNull(value)` - Checks if a value is null.
- `assertNotNull(value)` - Checks if a value is not null.
- `assertThrows(Exception.class, () -> code)` - Verifies an exception is thrown.
- `assertTimeout(Duration.ofMillis(100), () -> method())` - Ensures a method executes within a time limit.
- `assertAll("Grouped Assertions", ...)` - Groups multiple assertions together.

### 1.3. Assumptions
- Assumptions allow tests to be skipped based on runtime conditions.
	- `assumeTrue(condition)` - Skips test if the condition is false.
	- `assumeFalse(condition)` - Skips test if the condition is true.
	- `assumingThat(condition, executable)` - Runs the executable only if the condition is true.

### 1.4. Dynamic tests
- In traditional unit testing, test methods are static—each test method is defined in advance and executed by the test runner. However, in some scenarios, it is beneficial to create test cases dynamically based on input data, configurations, or runtime logic.
- Dynamic tests in JUnit 5 allow you to generate test cases programmatically and use collections, streams, or other data sources to provide test data dynamically.
- The dynamic test factory generates test cases on demand during runtime, rather than predefining them and it also permits the use of functional programming concepts (lambdas, streams) to simplify test definitions.
- Dynamic test example:
	`@TestFactory`
	`Collection<DynamicTest> dynamicTests() {`
	    `return Arrays.asList(`
	        `DynamicTest.dynamicTest("Addition Test", () -> assertEquals(4, 2 + 2)),`
	        `DynamicTest.dynamicTest("String Starts With", () -> assertTrue("hello".startsWith("h")))`
	    `);`
	`}`
	- Explanation: `@TestFactory` marks a method as a dynamic test factory. The method must return a collection or stream of `DynamicTest` instances. Unlike `@Test` methods, test factory methods do not have parameters.
	
- Valid Return Types for `@TestFactory`:
	- `Collection<DynamicTest>`
	- `List<DynamicTest>`
	- `Stream<DynamicTest>`
	- `Iterable<DynamicTest>`
	- `DynamicTest[]`
	
- Dynamic test example from data structure:
	`@TestFactory`
	`Collection<DynamicTest> testFromList() {`
	    `List<String> words = Arrays.asList("JUnit", "Test", "Framework");`

	    return words.stream()
	        .map(word -> DynamicTest.dynamicTest("Testing word: " + word, () -> 
	            assertTrue(word.length() > 2)))
	        .toList();
	`}
	
- Dynamic tests vs. Parameterized tests

| Feature         | Dynamic tests                 | Parameterized test (`@ParameterizedTest`) |
| --------------- | ----------------------------- | ----------------------------------------- |
| Test creation   | Generated at runtime          | Defined statically                        |
| Data source     | Any (files, DB, APIs)         | Limited (CSV, enums, arrays)              |
| Flexibility     | High                          | Moderate                                  |
| Recommender for | Large-scale, data-driven test | Simple parameterized scenarios            |

### 1.5. Integrations with Spring Boot

| Test type             | Annotation         | Scope                                            |
| --------------------- | ------------------ | ------------------------------------------------ |
| Integration testing   | `@SpringBootTest`  | Loads the full application context               |
| Web layer testing     | `@WebMvcTest`      | Loads only web-related beans (e.g. Controllers)  |
| Service layer testing | `@Test` with mocks | Tests service methods with mocked dependencies   |
| Data layer testing    | `@DataJpaTest`     | Loads only JPA repositories and configurations   |
| Mocking components    | `@MockBean`        | Mocks beans inside Spring's `ApplicationContext` |
1. The `@SpringBootTest` annotation is used for full integration tests that load the entire Spring application context. The annotation loads the full Spring Boot application context, the `contextLoads()` test ensures that the application starts without any failures. Useful for high-level integration tests. 
	- The loaded context can also be limited by specifying properties. Example:
		- `@SpringBootTest(properties = {"spring.profiles.active=test"})` - this loads the application with a specific profile (`test`). We can also just use `@ActiveProfiles("test")` to load a specific profile.
2. For testing only the web layer (i.e., controllers), use `@WebMvcTest`. This avoids loading the entire Spring Boot application context, improving performance.
	- `@WebMvcTest(Controller.class)` loads only the specified controller and its dependencies (e.g., `@Service`, `@Repository` are not loaded).
	- `MockMvc` is used for simulating HTTP requests without starting a real server.
	- No database connection is required, making tests faster.
	- Since `@WebMvcTest` only loads the web layer, services should be mocked using `@MockBean`
3. For testing service logic, we can use standard JUnit 5 tests with Mockito (use `@Mock` to mock the repository and `@InjectMocks` to inject the mock into the service).
4. To test the database interactions, we can use `@DataJpaTest`.
	- `@DataJpaTest` loads only JPA-related components (Repositories, Hibernate, EntityManager).
	- Uses an in-memory database (H2 by default).
	- Rolls back transactions automatically after each test.
	- A specific database can also be used instead of H2 - `@DataJpaTest(properties="spring.datasource.url=jdbc:mysql://localhost:3306/testdb")`
5. `@MockBean` is used inside Spring Boot tests to replace beans in the `ApplicationContext`. It is often used with `@SpringBootTest` or `@WebMvcTest`. Useful when testing services without connecting to a database.

### 1.6. JUnit 5 Best Practices
1. Use meaningful test names with `@DisplayName`.
2. Write independent tests—avoid shared state between tests.
3. Use `@Nested` classes to group related tests.
4. Use parameterized tests to reduce duplicate test methods.
5. Prefer assertions over print statements.
6. Use assumptions when certain tests should only run under specific conditions.
7. Mock dependencies with Mockito to isolate unit tests.
8. Use `@Tag` annotations to categorize and filter tests.

### 1.7. JUnit 4 vs. JUnit 5

| Feature             | JUnit 4                                                            | JUnit 5                                                                                       |
| ------------------- | ------------------------------------------------------------------ | --------------------------------------------------------------------------------------------- |
| Annotations         | `@Test`, `@Before`, `@After`, `@BeforeClass`, `@AfterClass`        | `@Test`, `@BeforeEach`, `@AfterEach`, `@BeforeAll`, `@AfterAll`, `@Nested`, `@Tag`            |
| Test Runners        | `@RunWith` is required for extensions                              | Uses `@ExtendWith` for extensions                                                             |
| Parameterized Tests | Requires external runner (`@RunWith(Parameterized.class)`)         | Built-in support with annotations like `@ParameterizedTest`, `@ValueSource`, `@CsvSource`     |
| Assertions          | `Assert` class with static methods (e.g., `Assert.assertEquals()`) | Assertions class with more expressive methods, including `assertAll()` for grouped assertions |
| Dynamic Tests       | Not available                                                      | Available with `@TestFactory` to create dynamic tests programmatically.                       |


## 2. Mockito
Mockito is useful for writing unit tests where real dependencies (e.g., database, API calls) need to be replaced with mock objects. Key advantages include:
- Isolation - tests focus only on the unit under test, without external dependencies.
- Performance - mocks are faster than real implementations (e.g., no database access).
- Reliability - no dependence on external services, preventing test flakiness.
- Flexibility - fine-grained control over method behaviors and verification.

### 2.1. Mocking
- Mocking refers to creating an object that simulates another object’s behavior. These objects do not execute real code but return predefined responses when called.
- `@Mock` - Creates a mock instance of the dependency.
- `@InjectMocks` - Injects mocks into the class under test.
- `@ExtendWith(MockitoExtension.class)` - Enables Mockito in JUnit 5.

### 2.2. Stubbing
- Stubbing is defining the behavior of a mocked method when it is invoked in a test. This allows controlling return values, exceptions, and method behavior.
- `when(...).thenReturn(...)` - Specifies a return value when a method is called.
- `when(...).thenThrow(...)` - Defines an exception to be thrown.

### 2.3. Spying
- A spy is a partial mock that allows real method execution but also tracks interactions. This is useful when you want to test actual behavior while verifying method calls.
- Unlike mocks, which return default values such as null, 0 or false, spies call the real method.
- Using `when(...).thenReturn(...)` on a spy calls the actual method, unlike `doReturn(...)`.

### 2.4. Verifying
- Verification ensures that specific interactions with mocks occurred. This is useful for checking method calls, argument values, invocation counts, and order of execution.
- `times(n)` - Ensures a method was called exactly `n` times.
- `never()` - Ensures a method was never called.
- `atLeast(n)` - Ensures the method was called at least `n` times.
- `atMost(n)` - Ensures the method was called at most `n` times.

### 2.5. Argument captors
- Argument captors are a powerful feature in Mockito that allows you to capture and assert the values of method arguments passed to mocked methods. This is useful when you want to verify that specific values were passed to your mocks, especially when the values are complex objects or parameters that need further inspection.
- `ArgumentCaptor` works by capturing arguments passed to a mock method when the method is invoked. You define the type of the argument you'd like to capture, and later retrieve the captured value for assertions.
- Basic example:
	`ArgumentCaptor<String> captor = ArgumentCaptor.forClass(String.class);`
	`verify(mock).someMethod(captor.capture());` 
	`assertEquals("expected", captor.getValue());`
- Example for capturing multiple arguments:
	`ArgumentCaptor<String> captor = ArgumentCaptor.forClass(String.class);`
	`verify(mock, times(2)).someMethod(captor.capture());`
	
	`List<String> capturedArguments = captor.getAllValues();`
	`assertEquals("first", capturedArguments.get(0));`
	`assertEquals("second", capturedArguments.get(1));`

### 2.6. Mocking void methods
- Mockito provides `doNothing()`, `doThrow()`, and `doAnswer()` for mocking `void` methods:
- `doNothing()` - by default, mock methods do nothing, but it can be explicitly defined for clarity.
- `doThrow()` - simulates an exception being thrown when a method is called.
- `doAnswer()` - allows the definition of custom behavior for void methods. We can also execute arbitrary code inside the method using `doAnswer()`.
- ==Why not use `when()`?== - The reason you use `do*()` methods for void methods is that `when()` does not work with methods that have no return type. `do*()` methods are specifically designed to mock methods that return void.

### 2.7. Mocking Static Methods (Mockito v3.4+)
- Static methods can be difficult to mock with traditional approaches because they belong to a class, not an instance. Starting from Mockito 3.4, it introduced support for mocking static methods with `MockedStatic`.
- Basic example:
	`try (MockedStatic<SomeStaticClass> mockedStatic = mockStatic(SomeStaticClass.class)) {`
	`mockedStatic.when(SomeStaticClass::someMethod).thenReturn("mocked");`
    `assertEquals("mocked", SomeStaticClass.someMethod());`
	`}`
	- Explanation:
		- `mockStatic(SomeStaticClass.class)` - Creates a mock of the static class.
		- `when(...).thenReturn(...)` - Defines the behavior of the static method `someMethod`.
		- `assertEquals("mocked", SomeStaticClass.someMethod())` - Verifies that the static method returns the mocked value.

### 2.8. Additional considerations
- Mockito traditionally couldn't mock final classes or private methods. However, with the `mockito-inline` extension, this capability is added. When the extension is added, final classes can now be mocked. To mock private methods, we need to use `spy()` combined with `doReturn()`. This allows invoking the private method in a controlled manner.
- Mockito supports stubbing asynchronous methods, such as those that return `CompletableFuture`, `Flux`, or `Mono` in reactive programming frameworks like Spring WebFlux.
	- For methods returning `CompletableFuture`, we can use `when()` to specify the result of the async call.
	- If we're working with `Mono` or `Flux` from Project Reactor (common in Spring WebFlux), we can use `Mono.just()` or `Flux.just()` to provide mock responses.


## 3. AssertJ
- AssertJ is a library that provides a rich set of fluent assertion methods for Java. It's widely used in unit testing to make assertions more readable, expressive, and powerful, enhancing the testing experience. AssertJ simplifies writing tests by improving the assertion syntax and providing advanced features that are more cumbersome with JUnit alone.
- One of AssertJ's most significant features is its fluent API, which allows for chaining assertions in a clear and readable way. The methods are designed to be human-readable, so the test code looks more like a natural language, improving maintainability and clarity.
- AssertJ has extensive support for collections, arrays, and streams. It provides a rich set of assertions that allow checking elements, sizes, and even properties within the collection.
- AssertJ simplifies the assertion of exceptions in the code, offering a more expressive way to validate exceptions compared to JUnit. It lets you assert that specific exceptions are thrown, as well as check the message, type, and cause of the exception.
- AssertJ includes support for `Optional` and `Date` types, making it easier to validate these in modern Java applications. For example, you can assert that an `Optional` contains a value or is empty, and you can check if dates match certain criteria.

### 3.1. Key components
1. **Basic assertions** - AssertJ provides basic assertions that check properties of objects, such as being equal, not null, or having specific values. These assertions can be chained to create more readable and expressive test code. A list of common assertions:
	- `isNotEmpty` - Asserts that the string is not empty.
	- `startsWith` - Asserts that the string starts with a particular prefix.
	- `endsWith` - Asserts that the string ends with a particular suffix.
	- `isEqualTo()` - Asserts that two objects are equal.
	- `isNull()` - Asserts that an object is null.
	- `isNotNull()` - Asserts that an object is not null.
2. **List Assertions** - AssertJ excels in working with collections, arrays, and streams, providing powerful assertions to ensure the contents of your collections meet specific criteria. Common assertions for collections include:
	- `contains()` - Asserts that the list contains one or more specified elements.
	- `doesNotContain()` - Asserts that the list does not contain a specified element.
	- `hasSize()` - Asserts that the list has the expected size.
	- `isEmpty()` - Asserts that the list is empty.
	- `hasSizeGreaterThan()`, `hasSizeLessThan()` - Asserts that the list has a size greater or less than a specific value.
	- Additional methods like `isSubsetOf()` and `hasDuplicates()` allow for more advanced testing scenarios.
3. **Exception Assertions** - AssertJ provides more expressive and detailed exception assertions than JUnit. You can check not only the type of the exception but also its message, cause, and any other properties. Common methods related to exception assertions:
	- `isInstanceOf()` - Asserts that the exception is of a specific type.
	- `hasMessageContaining()` - Asserts that the exception message contains a specific string.
	- `hasMessageStartingWith()` - Asserts that the exception message starts with a specific string.
	- `hasCauseInstanceOf()` - Asserts that the exception has a specific cause.
	- `hasNoCause()` - Asserts that the exception has no cause.
4. **Advanced Assertions**:
	- Object Properties - Assertions on individual fields of objects.
	- Arrays - Assertions on arrays, such as checking if they are sorted, or have the exact same content as another array.
	- String Assertions - Assertions on string properties like matching a regular expression.
	- Assertions on Optional - Verify whether an Optional contains a value or is empty.