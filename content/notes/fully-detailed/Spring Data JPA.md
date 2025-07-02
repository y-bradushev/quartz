## 1. Repository interfaces in Spring Data JPA
### 1.1. Overview
- Spring Data JPA provides several built-in repository interfaces that simplify database operations, allowing developers to focus on business logic instead of writing boilerplate data access code. These interfaces follow a layered architecture where higher-level repositories inherit features from lower-level ones. These interfaces are as follows:
	1. `CrudRepository<T, ID>` - The `CrudRepository` interface is the most basic repository provided by Spring Data JPA. It defines CRUD (Create, Read, Update, Delete) operations for a given entity type. Key features:
		- Generic interface that works with any entity.
		- Eliminates the need to write standard CRUD queries.
		- Provides methods for basic operations such as saving, deleting, and finding entities.
		- Does **not** provide support for pagination or sorting.
		- Works well for simple applications where only basic CRUD operations are needed.

	2. `PagingAndSortingRepository<T, ID>` - This interface extends `CrudRepository` and introduces pagination and sorting capabilities. Key features:
		- Supports large datasets by allowing results to be fetched in chunks.
		- Provides sorting capabilities on any entity field.
		- Supports sorting by multiple fields.
		- Works best when dealing with large data sets where pagination is necessary.

	3. `JpaRepository<T, ID>` - The `JpaRepository` interface extends `PagingAndSortingRepository` and provides additional JPA-specific functionality. Key features:
		- Includes batch operations (e.g., bulk delete).
		- Supports query customization.
		- Allows flushing changes manually for performance optimization.
		- `JpaRepository` is the most commonly used interface for Spring Data JPA repositories.
		- Useful when working with large datasets where batch processing is needed.
		- Provides better transactional support compared to `CrudRepository`.

### 1.2. Comprehensive list of important JPA repository methods
- ==**CRUD operations**==

| Method                                      | Description                                    | When to use                                                 |
| ------------------------------------------- | ---------------------------------------------- | ----------------------------------------------------------- |
| `save(S entity)`                            | Saves a new entity or updates an existing one. | Use when inserting or updating single entities.             |
| `saveAll(Iterable<S> entities)`             | Saves multiple entities in bulk.               | Use when performing batch inserts or updates.               |
| `findById(ID id)`                           | Retrieves an entity by its ID `(Optional<T>`). | Use when looking up a single entity by primary key.         |
| `existsById(ID id)`                         | Checks if an entity with the given ID exists.  | Before performing operations that require entity existence. |
| `findAll()`                                 | Retrieves all entities.                        | Avoid for large datasets -use pagination instead.           |
| `findAllById(Iterable<ID> ids)`             | Retrieves multiple entities by their IDs.      | When fetching multiple records at once.                     |
| `deleteById(ID id)`                         | Deletes an entity by its ID.                   | Use when deleting a specific entity.                        |
| `delete(T entity)`                          | Deletes a specific entity instance.            | Use when you have the entity object available.              |
| `deleteAll(Iterable<? extends T> entities)` | Deletes multiple entities in bulk.             | Efficient when deleting multiple records at once.           |
| `deleteAll()`                               | Deletes all entities from the table.           | Use with caution, only when clearing a table is necessary.  |

- **==Batch operations and flushing==**

| Method                                | Description                                             | When to use                                                                 |
| ------------------------------------- | ------------------------------------------------------- | --------------------------------------------------------------------------- |
| `flush()`                             | Synchronizes the persistence context with the database. | When you want to ensure changes are immediately written to the DB.          |
| `saveAndFlush(S entity)`              | Saves the entity and immediately flushes changes.       | Use when changes must be committed immediately to avoid stale state issues. |
| `deleteInBatch(Iterable<T> entities)` | Deletes entities in bulk without fetching them first.   | Efficient for batch deletions where entities are identified by ID.          |
| `deleteAllInBatch()`                  | Deletes all records from the table in a single query.   | Faster than `deleteAll()` since it doesn't load entities into memory.       |

- **==Pagination and sorting==**

| Method                       | Description                                                | When to use                                    |
| ---------------------------- | ---------------------------------------------------------- | ---------------------------------------------- |
| `findAll(Pageable pageable)` | Retrieves a page of entities based on pagination settings. | When handling large datasets efficiently.      |
| `findAll(Sort sort)`         | Retrieves all entities sorted by the given criteria.       | When results need ordering without pagination. |

- **==JPA Criteria API support==**

| Method                                              | Description                                        | When to use                                             |
| --------------------------------------------------- | -------------------------------------------------- | ------------------------------------------------------- |
| `findAll(Specification<T> spec)`                    | Executes a query using the JPA Criteria API.       | When building complex dynamic queries programmatically. |
| `findAll(Specification<T> spec, Pageable pageable)` | Executes a paginated query using the Criteria API. | When needing both dynamic filtering and pagination.     |
| `findAll(Specification<T> spec, Sort sort)`         | Executes a sorted query using the Criteria API.    | When needing dynamic filtering with sorting.            |


## 2. Custom query methods
- Spring Data JPA provides multiple ways to define and execute custom queries:
	1. **Derived query** methods - Spring automatically derives queries based on method names. Supported keywords are the following: `findBy`, `countBy`, `existsBy`, `deleteBy`, `orderBy`
	2. **JPQL (Java Persistence Query Language) query** - Uses @Query annotation to define queries in repository methods. Supports parameterized queries using ?1 or named parameters. 
		- Example:
			`@Query("SELECT u FROM User u WHERE u.email = ?1")`
			`User findByEmail(String email);`
	3. **Native SQL query** - Uses raw SQL with `nativeQuery = true`.
		- Example:
			`@Query(value = "SELECT * FROM users WHERE email = ?1", nativeQuery = true)`
			`User findByEmailNative(String email);`
	4. **Named parameters** - example:
			`@Query("SELECT u FROM User u WHERE u.email = :email")`
			`User findByEmail(@Param("email") String email);`
	5. **Criteria API (Dynamic Queries)** - Useful for dynamic and complex queries. Avoids hardcoding field names. Uses `Specification<T>` from Spring Data JPA.
	6. `@NamedQuery` / `@NamedNativeQuery` - these annotations also exist for raw SQL/JPQL 


## 3. Entity manager
- The `EntityManager` is the core interface in JPA responsible for managing the persistence lifecycle of entities. In Spring Data JPA, `EntityManager` is abstracted away in standard repositories, but when implementing custom repository logic, `EntityManager` has to be used directly.
- The most common use cases for the `EntityManager` would be the following:
	- Complex queries that require dynamic conditions.
	- Fetching partial entities (projections).
	- Bulk updates/deletes that need to bypass entity state tracking.
	- Stored procedures and native queries.
- Key responsibilities of the `EntityManager`: 
	- `persist(entity)` - Inserts a new entity into the database.
	- `merge(entity)` - Updates an entity.
	- `remove(entity)` - Deletes an entity.
	- `find(entityClass, id)` - Retrieves an entity by ID.
	- `createQuery(queryString, resultClass)` - Creates a JPQL query.
	- `createNativeQuery(queryString)` - Executes a native SQL query.
	- `flush()` - Synchronizes persistence context with the database.
	- `clear()` - Detaches all managed entities to prevent caching issues.


## 4. Transactions
### 4.1. Overview
- ==Transactions== in Spring Data JPA ensure data consistency and integrity by treating a sequence of database operations as a single unit of work. Spring provides declarative transaction management using the `@Transactional` annotation, allowing the efficient management of transactions without dealing with low-level database transaction APIs.
- Spring Data JPA automatically manages transactions in repository methods. Methods in `CrudRepository` and `JpaRepository` are transactional by default - `save()`, `delete()`, `findById()`, etc., operate inside a transaction without explicit `@Transactional` annotation. Read-only queries are optimized automatically (but can be further improved with `@Transactional(readOnly = true)`).

### 4.2. Declarative Transaction Management (@Transactional)
- `@Transactional` is used at the service layer to manage transactions explicitly. It provides control over how transactions behave. The entire method runs within a transaction. If an exception occurs, the transaction rolls back automatically. If `@Transactional` is omitted, multiple database operations execute independently, which might lead to data inconsistency.
- The `@Transactional` annotation supports several attributes to define transaction behavior:
	- **Read-Only Transactions** (`readOnly = true`) - Optimizes performance by preventing unnecessary changes to the database. Prevents dirty checking and flush operations. Suitable for SELECT queries. Spring skips the persistence context flush, improving performance.
	- **Rollback and Exception Handling** (`rollbackFor` / `noRollbackFor`) - By default, Spring rolls back transactions only for unchecked exceptions (`RuntimeException` or `Error`). To roll back for checked exceptions, we can use (`rollbackFor = Exception.class`). To prevent rollback for certain exceptions, we can use (`noRollbackFor = SpecificException.class`).
	- **Timeout** - Defines maximum time (in seconds) a transaction can run before being automatically rolled back. `@Transactional(timeout = 10)` - rolls back if execution exceeds 10 seconds.
	- **Propagation** - Determines how the method participates in an existing transaction. Several types exist:
		- REQUIRED (Default)	- Uses existing transaction or creates a new one if none exists.
		- REQUIRES_NEW - Always starts a new transaction, suspending any existing one.
		- MANDATORY - Requires an existing transaction; throws an exception if none exists.
		- NESTED - Creates a nested transaction within an existing transaction.
		- SUPPORTS - Runs within a transaction if one exists, otherwise runs non-transactionally.
		- NOT_SUPPORTED - Runs outside of any transaction, suspending any active one.
		- NEVER - Throws an exception if an active transaction exists.
	- **Isolation** - Defines how much one transaction is isolated from others. Several levels exist:
		- DEFAULT - Uses the database's default isolation level.
		- READ_UNCOMMITTED - Allows dirty reads (lowest isolation, highest performance).
		- READ_COMMITTED - Prevents dirty reads, but allows non-repeatable reads.
		- REPEATABLE_READ - Prevents dirty & non-repeatable reads, but allows phantom reads.
		- SERIALIZABLE - Fully isolates transactions, but may reduce performance.


## 5. Lazy vs. Eager loading
- When dealing with relational data in JPA/Hibernate, you often have to decide how to load related entities. This is managed using the fetch attribute in annotations like `@OneToMany`, `@ManyToOne`, `@OneToOne`, and `@ManyToMany`.

### 5.1. Lazy Loading
- Lazy loading is the default mode with the following characteristics:
	- Related entities are not loaded when the parent entity is fetched.
	- The related entities are loaded only when explicitly accessed.
	- Improves performance by avoiding unnecessary database queries.
	- Can lead to the `LazyInitializationException` if accessed outside the persistence context (e.g., after a session is closed). There are several approaches to avoid that exception:
		- Fetch data within an open session -  Use `@Transactional` at the service layer. Example:
			`@Transactional`
			`public List<Order> getOrdersForUser(Long userId) {`
			    `User user = userRepository.findById(userId).orElseThrow();`
			    `return user.getOrders(); // Works fine as session is open`
			`}`
		- Use `JOIN FETCH` (JPQL) - example:
			`@Query("SELECT u FROM User u JOIN FETCH u.orders WHERE u.id = :userId")`
			`User findUserWithOrders(@Param("userId") Long userId);`
		- Use DTO projections - example:
			`interface UserOrdersDTO {`
			    `String getUserName();`
			    `List<Order> getOrders();`
			`}`

			`@Query("SELECT new com.example.UserOrdersDTO(u.name, o) FROM User u JOIN u.orders o WHERE u.id = :userId")`
			`List<UserOrdersDTO> findUserOrders(@Param("userId") Long userId);`

### 5.2. Eager loading
- Related entities are immediately fetched along with the parent entity.
- Can lead to the N+1 problem if not handled properly - the N+1 problem happens when fetching multiple parent entities, each lazy-loaded collection may trigger additional queries. 
- Suitable when related data is always needed but it can cause a performance bottleneck by fetching unnecessary data as it can slow down response times and increase memory consumption.


## 6. Caching
- Caching improves performance by reducing the need for repeated database queries.
- Spring Data JPA has several levels, first one being the First-Level Cache (L1):
	- Enabled by default
	- Scoped to a Hibernate session
	- Caches entities within the same transaction/session
	- Once the session is closed, cache is cleared
- The next one is the Second-Level Cache (L2):
	- Shared across sessions
	- Requires external caching providers like EhCache, Caffeine, or Redis.
	- Must be explicitly enabled.
- The last one is query caching:
	- Caches the results of queries.
	- Must be explicitly enabled.


## 7. Auditing
- Spring Data JPA provides auditing to automatically track creation and modification timestamps.
- To enable auditing we need to add `@EnableJpaAuditing` annotation in a configuration class.
- We can then use auditing annotations in the entities, such as `@CreatedDate`, `@LastModifiedDate`, etc.
- Auditing additional fields (such as `createdBy`, `modifiedBy`, etc.) would require implementing the `AuditorAware` interface to determine the current user. 