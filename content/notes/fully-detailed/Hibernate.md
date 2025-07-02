## 1. Entity mapping
### 1.1. Overview
- Hibernate is a powerful Object-Relational Mapping (ORM) framework for Java applications, enabling developers to interact with databases using Java objects instead of raw SQL queries. Hibernate abstracts database interactions, reducing boilerplate code and improving maintainability.
- Entity mapping in Hibernate defines how Java objects (POJOs) correspond to database tables. Hibernate provides annotations and XML-based configuration to establish this mapping. While XML was used in older versions, annotations are the modern and preferred approach.
- An entity in Hibernate is a POJO annotated with `@Entity` (marking the class as a persistent entity). It must have:
	1. A unique identifier (`@Id`) - Specifies the primary key.
	2. Optional table-level configurations (`@Table`) - Maps the entity to a specific table.
	3. Column-level settings (`@Column`) - Customizes column properties (e.g., name, length, constraints).
	4. Getter and setter methods
- Hibernate allows two ways to access entity attributes:
	- Field Access: Annotations are placed on fields. Hibernate accesses fields directly (ignoring getters/setters).
	- Property Access: Annotations are placed on getters. Hibernate calls getter methods to access values.

### 1.2. Primary Key Mapping and Generation Strategies
- Each entity requires a primary key, which can be manually set or automatically generated.
- There are several primary key generation strategies:
	- IDENTITY: Uses the database’s auto-increment feature.
	- SEQUENCE: Uses a database sequence.
	- TABLE: Uses a separate table for key generation.
	- AUTO: Delegates key generation to the database dialect.

### 1.3. Mapping Relationships
- Hibernate supports different types of relationships:
	- **One-to-One** (`@OneToOne`) - A single instance of one entity is associated with a single instance of another entity.
	- **One-to-Many** (`@OneToMany`) - One entity is related to multiple entities. Example:
		`@Entity`
		`public class User {`
		    `@OneToMany(mappedBy = "user", cascade = CascadeType.ALL, orphanRemoval = true)`
		    `private List<Order> orders;`
		`}`
	- In the above example:
		- `mappedBy`: Defines the owning side (inverse side of the relationship).
		- `cascade`: Determines how changes propagate.
		- `orphanRemoval = true`: Deletes child records if they are removed from the collection.
	- **Many-to-One** (`@ManyToOne`) - Multiple entities refer to a single entity.
	- **Many-to-Many** (`@ManyToMany`) - An entity can be associated with multiple entities, and vice versa. A join table is used to store relationships.

### 1.4. Embedded objects
- An embedded object is a value-type object that is not an entity and does not have an `@Id` or a separate table. Instead, its attributes are stored in the table of the owning entity. For example, an `Address` object can be embedded within multiple entities like `User` and `Company` instead of creating a separate `Address` entity.
- To use embedded objects, Hibernate provides two annotations:
	1. `@Embeddable` → Marks a class as embeddable.
	2. `@Embedded` → Marks a field in an entity as an embedded object.
- Embedded objects can contain relationships (`@OneToOne`, `@ManyToOne`), but they cannot be entities themselves.
- By default, Hibernate uses the field names of the embedded class as column names. That can be overridden using the `@AttributeOverrides` annotation.

### 1.5. Inheritance mapping
- Hibernate supports three strategies:
	1. **Single Table Strategy** (`@Inheritance(strategy = InheritanceType.SINGLE_TABLE)`) - Uses one table for all subclasses. Discriminator column differentiates types. 
		- Example:
			`@Entity`
			`@Inheritance(strategy = InheritanceType.SINGLE_TABLE)`
			`@DiscriminatorColumn(name = "user_type", discriminatorType = DiscriminatorType.STRING)`
			`public class User { }`

	2. **Joined Table Strategy** (`@Inheritance(strategy = InheritanceType.JOINED)`) - Uses separate tables for each subclass. A join is required to fetch data.
		- Example:
			`@Entity`
			`@Inheritance(strategy = InheritanceType.JOINED)`
			`public class User { }`

	3. **Table Per Class** (`@Inheritance(strategy = InheritanceType.TABLE_PER_CLASS)`) - Each class has its own table. No polymorphic queries.
		- Example:
			`@Entity`
			`@Inheritance(strategy = InheritanceType.TABLE_PER_CLASS)`
			`public class User { }`

### 1.6. Cascade Types
- Cascade defines operations that propagate from parent to child:
	- `CascadeType.ALL`: Applies all operations.
	- `CascadeType.PERSIST`: Saves child entities when the parent is saved.
	- `CascadeType.MERGE`: Merges child entities when the parent is merged.
	- `CascadeType.REMOVE`: Deletes child entities when the parent is deleted.
	- `CascadeType.REFRESH`: Refreshes child entities when the parent is refreshed

### 1.7. Best practices for entity mapping
- Use lazy loading to prevent unnecessary queries.
- Avoid EAGER fetching for collections.
- Use DTOs (Data Transfer Objects) to optimize queries.
- Properly index foreign keys for performance.
- Use cascade operations carefully to avoid unintended deletes.


## 2. Hibernate Query Language (HQL)
### 2.1. Definition
- HQL is an object-oriented query language that operates on entity objects instead of database tables. It is similar to SQL but understands entity names and relationships, making it more expressive and aligned with Java’s object-oriented paradigm.

### 2.2. Basic HQL queries
- **Fetching All Records** - HQL allows retrieving all records of an entity without specifying column names.
	- Example:
		`Query query = session.createQuery("FROM User");`
		`List<User> users = query.list();`
	- Explanation:
		- `"FROM User"` is equivalent to `SELECT * FROM users` in SQL.
		- It retrieves all records from the User entity.
		- list() returns a list of User objects.
- **Fetching Records with Conditions** - Fetching a record based on a specific condition.
	- Example:
		`Query query = session.createQuery("FROM User WHERE username = :username");`
		`query.setParameter("username", "johndoe");`
		`User user = (User) query.uniqueResult();`
	- Explanation:
		- `:username` is a named parameter to prevent SQL injection.
		- `uniqueResult()` returns a single result or `null` if no match is found.
- **Fetching Specific Columns** - HQL allows selecting specific columns instead of entire entities.
	- Example:
		`Query query = session.createQuery("SELECT u.username, u.email FROM User u");`
		`List<Object[]> results = query.list();`

		`for (Object[] row : results) {`
		    `System.out.println("Username: " + row[0] + ", Email: " + row[1]);`
		`}`
	- Explanation:
		- This returns an array of objects, each containing selected fields.

### 2.3. Using Aggregation Functions
- HQL supports aggregation functions similar to SQL:
	1. **Counting Rows** - `"SELECT COUNT(u) FROM User u"` - returns the total number of User records.
	2. **Calculating Average** - `"SELECT AVG(o.totalAmount) FROM Order o"` - Returns the average order amount.
	3. **Finding Maximum & Minimum** - `"SELECT MAX(o.totalAmount) FROM Order o"` or `"SELECT MIN(o.totalAmount) FROM Order o"` - Returns the maximum/minimum values.
	4. **Summing Values** - `"SELECT SUM(o.totalAmount) FROM Order o"` - Returns the total sum of the specified field.

### 2.4. Joins in HQL
- HQL supports joins like SQL, but it uses entity relationships instead of foreign keys:
	1. **Inner Join** example:
		`Query query = session.createQuery(`
		    `"SELECT u.username, o.totalAmount FROM User u JOIN u.orders o WHERE o.totalAmount > :amount");`
		`query.setParameter("amount", 1000);`
		`List<Object[]> results = query.list();`
		
		- Equivalent to `INNER JOIN` in SQL.
		- Retrieves users and their orders where the `totalAmount` is greater than 1000.

	2. **Left Join (Fetch)** example:
		`Query query = session.createQuery("FROM User u LEFT JOIN FETCH u.orders");`
		`List<User> users = query.list();`
		
		- Equivalent to `LEFT JOIN`.
		- `FETCH` ensures that related entities are loaded immediately to avoid `LazyInitializationException`.

	3. **Right join** example:
		`Query query = session.createQuery("FROM Order o RIGHT JOIN o.user u");`
		`List<Object[]> results = query.list();`
		
		- Equivalent to `RIGHT JOIN`.

	4. **Full join** - HQL does not support `FULL JOIN`. You need to use native SQL for that.

### 2.5. Native SQL queries
- Sometimes, HQL is not sufficient, and we need raw SQL queries. Example:
	`Query query = session.createSQLQuery("SELECT * FROM users").addEntity(User.class);`
	`List<User> users = query.list();`
	- `createSQLQuery()` - allows direct SQL execution.
	- `addEntity(User.class)` - maps each row to a User object.

- It is also possible to map results manually. Example:
	`Query query = session.createSQLQuery("SELECT username, email FROM users");`
	`List<Object[]> results = query.list();`
	- This approach returns raw object arrays, requiring manual extraction.

### 2.6. Named queries
- HQL supports named queries, which are predefined queries stored in entity classes for reuse.
- Named queries improve maintainability and reduce redundant query definitions.
- Example:
	`Query query = session.getNamedQuery("User.findByUsername");`
	`query.setParameter("username", "johndoe");`
	`User user = (User) query.uniqueResult();`


### 2.7. Pagination, subqueries, updating and deleting
#### 2.7.1. Pagination
- HQL supports pagination using `setFirstResult()` and `setMaxResults()`.
- Example:
	`Query query = session.createQuery("FROM User");`
	`query.setFirstResult(10);  // Skip first 10 records`
	`query.setMaxResults(5);   // Fetch next 5 records`
	`List<User> users = query.list();`

#### 2.7.2. Using subqueries
- HQL allows subqueries to fetch data conditionally.
- Example:
	`Query query = session.createQuery("FROM User u WHERE u.id IN (SELECT o.user.id FROM Order o WHERE o.totalAmount > 1000)");`
	`List<User> users = query.list();`

#### 2.7.3. Updating and Deleting
- Unlike SQL, HQL supports bulk updates and deletes.
- Update example:
	`Query query = session.createQuery("UPDATE User SET status = 'ACTIVE' WHERE lastLogin < :date");`
	`query.setParameter("date", LocalDate.now().minusDays(30));`
	`int updated = query.executeUpdate();`
- Delete example:
	`Query query = session.createQuery("DELETE FROM User WHERE status = 'INACTIVE'");`
	`int deleted = query.executeUpdate();`


## 3. Caching
- Caching in Hibernate enhances application performance by reducing redundant database queries. It stores frequently accessed objects in memory, minimizing expensive database hits.
- Hibernate provides three levels of caching:
	1. First-Level Cache (Session Scope)
	2. Second-Level Cache (SessionFactory Scope)
	3. Query Cache (For caching query results)

### 3.1. First-Level Cache (Session Scope)
- Enabled by default in Hibernate - no explicit configuration is needed.
- Only exists within a single session.
- If an entity is already in the session, Hibernate returns it from the cache instead of querying the database again.
- The cache gets cleared when the session is closed - once the session is closed, the cache is lost.
- Reduces redundant queries within a single transaction.

### 3.2. Second-Level Cache (SessionFactory Scope)
- Unlike the first-level cache, the second-level cache is shared across multiple sessions.
- Must be explicitly enabled and configured - To enable second-level caching, the following property has to be added to the `hibernate.cfg.xml`:
	`<hibernate-configuration>`
	    `<session-factory>`
	        `<property name="hibernate.cache.use_second_level_cache">true</property>`
	        `<property name="hibernate.cache.region.factory_class">`
	            `org.hibernate.cache.ehcache.EhCacheRegionFactory`
	        `</property>`
	    `</session-factory>`
	`</hibernate-configuration>`
- Useful for long-lived objects that are frequently accessed.
- On the entity-level, it has to be enabled using Hibernate annotations (`@Cache(usage = CacheConcurrencyStrategy.READ_WRITE)`)
- Hibernate supports multiple cache providers, with each of them having different performance characteristics. EhCache, Caffeine, and Redis are widely used in Spring Boot applications. Some other ones are Hazelcast and Infinispan.
- Cache concurrency strategies define how cache data is managed when multiple transactions access it:
	- `READ_ONLY` - Use for immutable entities (e.g., lookup tables).
	- `NONSTRICT_READ_WRITE` - Allows some stale data; updates take effect asynchronously.
	- `READ_WRITE` - Uses a timestamp-based approach to maintain consistency.
	- `TRANSACTIONAL` - Full ACID transactions; requires a JTA transaction manager.

### 3.3. Query Cache
- Caches the results of HQL/Criteria queries, not individual entities.
- It is used in combination with second-level cache so a requirement is for the second-level cache to be enabled.
- Works well for frequently executed, read-only queries.
- To enable query caching in, the following needs to be added to `hibernate.cfg.xml`:
	`<property name="hibernate.cache.use_query_cache">true</property>`
- It is also possible to enable it for specific queries by setting the `query.setCacheable(true);` property
- Query cache does not store entity objects, only the IDs of results.
- Useful for expensive queries that do not change often.