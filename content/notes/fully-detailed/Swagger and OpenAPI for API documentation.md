## 1. Definition
- ==Swagger== is a suite of tools used for documenting, designing, testing, and consuming RESTful APIs.
- ==The OpenAPI Specification (OAS)== is a standard format for describing REST APIs in a machine-readable way.
- Originally, Swagger was both a specification and a set of tools. However, the specification was later renamed to OpenAPI Specification (OAS) when it became part of the OpenAPI Initiative (OAI) under the Linux Foundation.
- Despite the renaming, Swagger tools (Swagger UI, Swagger Editor, Swagger Codegen, etc.) are still widely used.
- Spring Boot integrates Swagger via SpringDoc OpenAPI library. It automatically generates OpenAPI documentation from controllers and models and provides an interactive UI to explore and test API endpoints.

## 2. Benefits
- Using Swagger/OpenAPI in API development offers several benefits:
	1. **Clear API Contracts** - Defines a structured format for API requests and responses. Reduces ambiguity in API behavior, making it easier for consumers to integrate.
	2. **Automated API Documentation** - Generates interactive documentation automatically from API annotations or YAML/JSON definitions. Enables developers and testers to explore APIs easily.
	3. **Code Generation** - Can generate server stubs and client SDKs in multiple programming languages. Reduces boilerplate code and speeds up development.
	4. **Validation and Testing** - Ensures compliance with the API contract. Can be integrated with tools like Postman, RestAssured, and Swagger Inspector for API testing.
	5. **API Mocking** - Can generate mock servers based on the API contract, allowing frontend teams to start development before backend implementation is complete.

## 3. Key components of OAS
- The OpenAPI Specification provides a standard way to describe RESTful APIs. Key components include:
	1. **Info Object** - Defines API metadata such as title, description, and version.
		- Example: 
			`openapi: 3.0.0`
			`info:`
			  `title: My API`
			  `description: API for managing users`
			  `version: 1.0.0`

	2. **Servers** - Specifies the base URLs for different API environments.
		- Example:
			`servers:`
			  `- url: https://api.example.com/v1`
			    `description: Production Server`
			  `- url: https://staging-api.example.com`
			    `description: Staging Server`

	3. **Paths** - Defines API endpoints and available HTTP operations.
		- Example:
			`paths:`
			  `/users:`
			    `get:`
			      `summary: Get all users`
			      `responses:`
			        `'200':`
			          `description: List of users`
			          `content:`
			            `application/json:`
			              `schema:`
			                `type: array`
			                `items:`
			                  `$ref: '#/components/schemas/User'`

	4. **Components** - Reusable schemas, parameters, and security definitions.
		- Example:
			`components:`
			  `schemas:`
			    `User:`
			      `type: object`
			      `properties:`
			        `id:`
			          `type: integer`
			        `name:`
			          `type: string`

	5. **Security** - Defines authentication mechanisms (JWT, OAuth, API keys).
		- Example:
			`components:`
			  `securitySchemes:`
			    `BearerAuth:`
			      `type: http`
			      `scheme: bearer`
			`security:`
			  `- BearerAuth: []`


## 4. Swagger tools

| Tool              | Description                                                                        |
| ----------------- | ---------------------------------------------------------------------------------- |
| Swagger UI        | Generates an interactive API documentation webpage from an OpenAPI definition.     |
| Swagger Editor    | A web-based editor for writing and validating OpenAPI definitions in YAML or JSON. |
| Swagger Codegen   | Generates server stubs and client SDKs in various languages.                       |
| Swagger Inspector | A cloud-based API testing tool for quickly validating API endpoints.               |

## 5. Best practices for API documentation with Swagger/OpenAPI
- **Keep It Up to Date** - Ensure the OpenAPI document reflects the latest version of the API. Automate documentation updates using CI/CD pipelines.
- **Use Descriptive Endpoints** - Avoid ambiguous names like `/getUserById`, prefer `/users/{id}`.
- **Define Request & Response Examples** - Include sample request and response payloads.
- **Include Security Details** - Specify authentication and authorization mechanisms.
- **Leverage Reusable Components** - Use the components section to avoid duplication in API definitions.
- **Versioning** - Define API versions in the info section or via URL (e.g., `/v1/users`). Use `x-api-version` headers for version negotiation.


