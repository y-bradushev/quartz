Q: What are joins in SQL?
A: Joins in SQL are used to combine rows from two or more tables, based on a related column between them. There are several types of joins:
1) Inner join - Returns rows when there is at least one match in both tables.
2) Left join (left outer join) - Returns all rows from the left table, and the matched rows from the right table. The result is NULL on the right side if there is no match.
3) Right join (right outer join) - Returns all rows from the right table, and the matched rows from the left table. The result is NULL on the left side if there is no match.
4) Full join (full outer join) - Returns rows when there is a match in one of the tables. It effectively combines the results of both LEFT JOIN and RIGHT JOIN.
5) Cross join - Returns a Cartesian product of the two tables, i.e., it joins every row of the first table with every row of the second table.
6) Self join - A self join is a regular join but the table is joined with itself.

Q: What is ORM and how does it help software development?
A: ORM stands for Object relational mapping, it is a programming technique used to convert data between incompatible type systems in object-oriented programming languages. It acts as an interface between an application and a relational database by converting data between the database's tables and the application's objects. It helps development in several ways:
1) It simplifies database interactions, by providing a high-level abstraction over relational database operations, minimizing the need for SQL boilerplate code and allowing the use of native syntax in the programming language being used.
2) It enhances portability by providing a way of generating database-independent code, making it possible for applications to be ported to different types of databases with minimal changes to the application code.
3) Since ORM revolves around mapping database tables to application objects, it promotes object-oriented design principles and patterns.
4) Automates schema migration, simplifying the process of an evolving database schema. 

Q: What are the core components and terms in Hibernate?
A: Hibernate is a powerful, high-performance ORM framework for Java that facilitates the mapping of Java classes to database tables and from Java data types to SQL data types. Here are some of the core components and terms used:
1) SessionFactory - SessionFactory is a heavyweight, thread-safe object that is created once per database during application initialization. It's used to create Session instances for interactions with the database.
2) Session - Session is a lightweight, non-thread-safe object that represents a single unit of work with the database, providing methods to perform CRUD operations and query data.
3) Transaction - Represents a single atomic unit of work that either completely succeeds or fails, maintaining data integrity.
4) Entity - A regular Java object (POJO - Plain Old Java Object) that is mapped to a database table. An entity represents a table in the database, and each entity instance corresponds to a row in that table.
5) Configuration - Represents the configuration of Hibernate and is used to build the SessionFactory. It includes database connection details, dialect information, and mapping file locations.
6) HQL (Hibernate Query Language) - An object-oriented query language, similar to SQL, but operates on Hibernate's entity objects rather than database tables.
7) Criteria API - Provides a programmatic way to build queries for retrieving entities. It is used to create complex searches dynamically.
8) Caching - Hibernate supports two levels of caching to optimize database access: first-level cache (Session cache) and second-level cache (SessionFactory cache).
9) Mappings - Hibernate mappings define how Java classes and properties map to database tables and columns, respectively. Mappings can be specified using XML configuration files or annotations in the entity class itself.

Q: What is the lifecycle of a persistent object?
A: In Hibernate, the persistent object lifecycle refers to the different states an entity can go through during its interaction with the database session. The primary states in the lifecycle of a persistent object are:
1) Transient - An object is in a transient state if it has just been instantiated using the new operator but has not yet been associated with a Hibernate session. Transient instances are not persisted to the database, and Hibernate doesn't manage them or recognize any changes to them. An object transitions from transient to persistent state when it is saved or persisted in a session.
2) Persistent - An object is in a persistent state when it is associated with a session. Hibernate tracks changes made to persistent objects and synchronizes these changes with the database at the end of the transaction, usually during the session flush or commit. The object can transition to detached when the session that the object is associated with is closed, or the object is explicitly evicted from the session, and it can transition to removed when it is marked for deletion in the session.
3) Detached - Once the session is closed, an object becomes detached. Hibernate no longer tracks changes to detached objects. However, a detached object still retains its persistent identity (primary key), and its state can be reattached to a new session at a later point. It can transition back to persistent when the detached object is re-associated with an active session, typically using methods like update(), saveOrUpdate(), merge(), or lock().
4) Removed - An object is considered removed if it has been marked for deletion in a session. It will be deleted from the database upon transaction commit or session flush. Once deleted, the object transitions back to the transient state (assuming the object instance is still in memory). It is no longer associated with any session and will not be tracked by Hibernate.

Q: What are the types of Hibernate relations?
A: The primary types of relationships in Hibernate are:
1) One-to-One - This relationship indicates that one row in a table is associated with exactly one row in another table. It's typically implemented using a shared primary key or a foreign key. An example could be a User entity and a UserProfile entity where each user has a unique profile.
2) One-to-Many (and Many-to-One) - This is the most common relationship where one row in a table can be associated with multiple rows in another table. A many-to-one relationship is essentially the opposite, where multiple rows in a table are associated with one row in another table. An example could be a User entity and a Post entity, where one user can have multiple posts but each post belongs to one user. The relationship is often managed through a foreign key in the many-side table pointing to the primary key of the one-side.
3) Many-to-Many - In this relationship, rows in a table can be associated with multiple rows in another table, and vice versa. This is typically implemented using a join table that holds foreign keys referencing the primary keys of the two associated tables. An example could be a Student entity and a Course entity, where a student can enroll in multiple courses and a course can have multiple students enrolled.

Q: What are the types of fetching strategy?
A: In Hibernate, fetching strategy is a way to define how Hibernate retrieves associated objects when you access a particular entity. There are two main types of fetching strategies:
1) Lazy Fetching (Lazy loading) - Hibernate defers the loading of the associated entities until they are explicitly accessed in the code. This is the default fetching strategy for @OneToMany and @ManyToMany associations. Best for associations where the related entities are not always needed or when dealing with large data sets. Can lead to the "N+1 selects problem" if the associated entities are accessed outside of the original session's context or if you need to access them after the session is closed, requiring additional queries to the database.
2) Hibernate loads the main entity and its associated entities simultaneously, regardless of whether the associated entities are immediately needed. This is the default fetching strategy for @OneToOne and @ManyToOne associations. Ideal for when the associated entities are almost always used together with the main entity, or when the dataset is relatively small. Can lead to performance issues due to the overhead of loading unnecessary data, especially when the associations link to large datasets or complex entity graphs.

Q: Explain the differences between Session and EntityManager in Hibernate.
A: Session is part of the Hibernate core API and provides CRUD operations, query execution, and transaction management. EntityManager is part of the JPA (Java Persistence API) specification. It is a higher-level API that abstracts away the underlying persistence mechanism and provides similar functionality to Session but follows the JPA standard.

Q: What is the difference between get() and load() methods in Hibernate?
A: get() returns the actual object or null if not found and eagerly fetches the object from the database. load() returns a proxy object, which may throw an ObjectNotFoundException if the entity is not found. It lazy fetches the object, loading it when accessed.

Q: What is the N+1 selects problem, and how can it be resolved in Hibernate?
A: N+1 selects problem occurs when an application executes N+1 queries to fetch data. One query for the main entity and N additional queries for associated entities. It can be resolved by using JOIN FETCH in HQL or JPQL to fetch associated entities in a single query or by configuring batch fetching in Hibernate to load multiple entities in a single query.

Q: What are the differences between first-level and second-level caches in Hibernate?
A: The first-level cache is associated with the Session object. It is mandatory and exists per session and ensures that within a session, the same object is not loaded multiple times. The second-level cache is associated with the SessionFactory object. It is optional and configurable, providing a cache that can be shared across sessions, reducing database load for frequently accessed data.

Q: What is optimistic locking, and how does it differ from pessimistic locking?
A: Optimistic Locking assumes that multiple transactions can complete without affecting each other, using versioning to detect conflicts. Pessimistic Locking assumes that conflicts are likely and locks data to prevent other transactions from accessing it, using database locks to ensure data integrity.

Q: What are the benefits and drawbacks of using native queries in Hibernate?
A: The benefits include direct execution of database-specific SQL for complex queries and performance optimizations through database-specific features. The drawbacks are a lack of portability across different databases, and bypassing Hibernate's automatic query generation and caching mechanisms.



















