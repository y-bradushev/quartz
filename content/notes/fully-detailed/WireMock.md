## 1. Overview
- WireMock is a powerful HTTP-based mocking tool designed to simulate external APIs in tests. Unlike @MockBean, which is used for mocking Spring Beans, WireMock helps mock RESTful services and enables reliable testing when dealing with APIs outside your control.
- It simulates external API responses, which makes it possible to test the application's behavior without depending on live external APIs.
- It ensures test reliability, avoiding flakiness caused by network issues, rate limits, or API downtime.
- It supports integration testing by helping validate how the application interacts with third-party APIs without actually calling them.
- It allows response stubbing, making it possible to predefine expected API responses, including status codes, headers, and JSON payloads.
- It facilitates fault tolerance testing by enabling simulation of network delays, API failures, and error responses to check how the system handles failures.
- WireMock works with any HTTP client, offering compatibility with `RestTemplate`, `WebClient`, `OkHttp`, `Feign`, etc.

## 2. Example - stubbing an external API in tests
```
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@AutoConfigureWireMock(port = 8089)  // WireMock will run on port 8089
public class ExternalServiceTest {

    @Autowired
    private TestRestTemplate restTemplate;

    @BeforeEach
    void setup() {
        // Stubbing an external API call
        stubFor(get(urlEqualTo("/api/external/user/1"))
            .willReturn(aResponse()
                .withStatus(200)
                .withHeader("Content-Type", "application/json")
                .withBody("{ \"id\": 1, \"name\": \"John Doe\", \"email\": \"john@example.com\" }")));
    }

    @Test
    void testExternalApiCall() {
        ResponseEntity<String> response = restTemplate.getForEntity("http://localhost:8089/api/external/user/1", String.class);

        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertTrue(response.getBody().contains("John Doe"));
    }
}
```

- Explanation
	- `@AutoConfigureWireMock(port = 8089)` - Configures WireMock to run on a specific port.
	- `stubFor(get(urlEqualTo("/api/external/user/1"))` - Defines a stubbed API endpoint with a mock JSON response.
	- Test Execution - Calls the mocked endpoint and asserts the expected response.


## 3. Key features
1. **Stubbing API Responses** - WireMock allows defining API stubs with specific responses.
	`stubFor(get(urlEqualTo("/api/external/user/2"))`
	    `.willReturn(aResponse()`
	        `.withStatus(200)`
	        `.withBody("{ \"id\": 2, \"name\": \"Jane Doe\" }")));`
			
2. **Request Matching** - Verify API requests based on path, method, headers, query parameters, or request bodies.
	`verify(getRequestedFor(urlEqualTo("/api/external/user/1"))`
	    `.withHeader("Accept", equalTo("application/json")));`
		
3. **Configuring Responses**:
	- Setting Response Headers:
		`.willReturn(aResponse()`
		    `.withHeader("Content-Type", "application/json")`
		    `.withBody("{ \"message\": \"Hello\" }"));`
	- Simulating Delays (Latency Injection):
		`.willReturn(aResponse()`
		    `.withFixedDelay(3000));  // Delay response by 3 seconds`
	- Returning Different Status Codes:
		`stubFor(get(urlEqualTo("/api/external/user/3"))`
			`.willReturn(aResponse()`
		        `.withStatus(404)`
		        `.withBody("User not found")));`
				
4. **Simulating API Failures**:
	`stubFor(get(urlEqualTo("/api/external/user/4"))`
	    `.willReturn(aResponse()`
	        `.withStatus(500)`
	        `.withBody("Internal Server Error")));`
			
6. **WireMock allows additional configurations via a `wiremock.properties` file**:
	`wiremock.server.port=8089`
	`wiremock.server.enable-browser-proxying=true`
	`wiremock.server.disable-request-logging=false`
	
7. **WireMock can also be run separately as a standalone server instead of embedded in tests:
	`java -jar wiremock-standalone-3.3.1.jar --port 8089`
	
8. **Simulating Stateful Behaviors**: 
	`stubFor(get(urlEqualTo("/api/orders/1"))`
	    `.inScenario("Order Processing")`
	    `.willReturn(aResponse().withBody("{\"status\": \"pending\"}"))`
	    `.whenScenarioStateIs(STARTED)`
	    `.willSetStateTo("Shipped"));`
		
	`stubFor(get(urlEqualTo("/api/orders/1"))`
	    `.inScenario("Order Processing")`
	    `.willReturn(aResponse().withBody("{\"status\": \"shipped\"}"))`
	    `.whenScenarioStateIs("Shipped"));`
		
9. **Proxying Real APIs with WireMock** - WireMock can act as a proxy for real APIs while allowing overriding of certain responses:
	`stubFor(get(urlMatching("/api/external/.*"))`
	    `.willReturn(aResponse().proxiedFrom("https://real-api.com")));`


## 4. WireMock vs. @MockBean

| Feature     | @MockBean                                        | WireMock                                          |
| ----------- | ------------------------------------------------ | ------------------------------------------------- |
| Mock Type   | Spring-managed beans (services, repositories)    | External REST APIs                                |
| Use Case    | Mocking dependencies inside Spring Boot          | Simulating HTTP-based external services           |
| Test Scope  | Unit & Integration Tests                         | Integration & End-to-End Tests                    |
| Integration | Works with Mockito                               | Works with HTTP clients (RestTemplate, WebClient) |
| Example     | Mocking `UserRepository` in a `UserService` test | Mocking a third-party API response                |
