## 1. SQL
- SQL is the standard language used to interact with relational databases. It consists of multiple subcategories:
	1. **Data Definition Language (DDL)** - DDL commands define and modify database structures. Common commands include:
		- **`CREATE TABLE`** – Creates a new table.
		- **`ALTER TABLE`** – Modifies an existing table (e.g., adding/removing columns).
		- **`DROP TABLE`** – Deletes a table.
		- **`TRUNCATE TABLE`** – Removes all records but keeps the structure.
	2. **Data Manipulation Language (DML)** - DML commands interact with data:
		- **`INSERT`** – Adds new records.
		- **`UPDATE`** – Modifies existing records.
		- **`DELETE`** – Removes records.
		- **`SELECT`** – Retrieves data.
	3. **Data Querying** - Used for retrieving and filtering data:
		- **`SELECT`** – Retrieves data.
		- **`WHERE`** – Filters records.
		- **`ORDER BY`** – Sorts results.
		- **`GROUP BY`** – Aggregates data.
		- **`HAVING`** – Filters aggregated results.
	4. **SQL Joins** - Joins combine data from multiple tables based on a related column:
		- **`INNER JOIN`** – Returns matching records from both tables.
		- **`LEFT JOIN`** – Returns all records from the left table and matching ones from the right.
		- **`RIGHT JOIN`** – Returns all records from the right table and matching ones from the left.
		- **`FULL JOIN`** – Returns all records from both tables.
	5. **Transactions & ACID Properties** - Transactions ensure data integrity and consistency:
		- **Atomicity** – Transactions are all-or-nothing (for example, if transferring money between accounts, both debit and credit operations must succeed together).
		- **Consistency** – Transactions maintain database integrity (A transaction cannot leave the database in an invalid state (e.g., account balance can't be negative).
		- **Isolation** – Transactions don’t interfere with each other (Two users updating the same row should not corrupt data).
		- **Durability** – Once a transaction is committed, it persists (Even after a power failure, committed transactions remain stored).


## 2. Comparison between MySQL, PostgreSQL, and Oracle
### 2.1. MySQL
- MySQL is an open-source RDBMS known for its performance, ease of use, and wide adoption.
- Features:
	- Fast read-heavy workloads (common in web applications).
	- Storage engines: MyISAM (fast but no transactions) vs. InnoDB (ACID-compliant).
	- Replication & Clustering: Master-Slave, Master-Master replication.
	- JSON Support: Stores JSON data types.
- Advantages:
	- High performance for read-heavy applications.
	- Large community support.
	- Simple to set up and deploy.
- Limitations:
	- Limited support for advanced SQL features like window functions (improved in MySQL 8).
	- No full compliance with ACID properties in MyISAM storage engine.

### 2.2. PostgreSQL
- PostgreSQL is an advanced, open-source RDBMS known for its extensibility and standards compliance.
- Features:
	- Full ACID compliance.
	- JSONB Support for NoSQL-like features.
	- MVCC (Multi-Version Concurrency Control) for better transaction handling.
	- Full-text search capabilities.
- Advantages:
	- Supports complex queries and indexing.
	- Strong data integrity and reliability.
	- Extensible (supports stored procedures in multiple languages).
- Limitations:
	- Slightly slower for simple read-heavy workloads compared to MySQL.
	- More complex setup and maintenance.

### 2.3. Oracle
- Oracle is a proprietary RDBMS widely used in enterprise environments.
- Features:
	- Partitioning & Indexing: Optimized for handling large datasets.
	- Advanced security: Row-level access control and encryption.
	- PL/SQL: Procedural language extension for SQL.
	- RAC (Real Application Clusters): High availability and scalability.
- Advantages:
	- Highly scalable and optimized for large datasets.
	- Strong security and enterprise-level features.
	- Efficient memory and resource management.
- Limitations:
	- Expensive licensing fees.
	- Requires extensive database administration skills.


## 3. Indexing & Performance Optimization
### 3.1. Types of indexes
- Indexes are database objects that improve query performance by reducing the number of rows scanned during search operations. They work like an optimized lookup table, allowing databases to quickly locate data.
- When a query searches for a specific value in a column without an index, the database performs a full table scan (i.e., it checks each row one by one). With an index, the database can locate the relevant rows much faster, often in logarithmic time (O(log N)) instead of linear time (O(N)).
- Indexes must be carefully chosen to avoid unnecessary overhead as they slow down inserts, updates, and deletes (since indexes need to be updated), as well as using up additional storage. 
- Indexes store pointers to actual table rows in a data structure (e.g., B-tree, Hash).
- Different types of indexes serve different purposes. Choosing the right one depends on the query patterns:
	1. **Primary Index**:
		- Automatically created on primary key columns.
		- Ensures each row in a table is uniquely identifiable.
		- Typically implemented as a clustered index (where table rows are physically sorted based on the index).
		- In PostgreSQL and MySQL (InnoDB), the primary key index is automatically a clustered index, meaning the table data is physically sorted based on it.

	2. **Unique Index**:
		- Ensures that values in a column (or set of columns) are unique.
		- Prevents duplicate values, but unlike primary indexes, they allow NULL values (depending on the DBMS).

	3. **Composite Index**:
		- An index on multiple columns together.
		- Useful when queries often filter by multiple columns.
		- The order of columns in the index matters.
		- Example:
			`CREATE INDEX idx_orders_customer_date ON Orders(customer_id, order_date);`
			- If queries frequently filter orders by `customer_id` and `order_date`, this index speeds them up.
			- The index `(customer_id, order_date)` can be used for queries filtering by `WHERE customer_id = ?` and `WHERE customer_id = ? AND order_date = ?` but it won’t optimize queries filtering only by `order_date = ?` (because the first column in the index must be used).


	4. **B-Tree Indexes** (Balanced Tree):
		- Default index type in most databases (MySQL, PostgreSQL, Oracle).
		- Efficient for range queries and ordered data retrieval.
		- Self-balancing structure: maintains an optimal search depth, ensuring fast lookups.
		- Used for: Searching with `<`, `>`, `=`, `BETWEEN`, sorting (`ORDER BY`), and indexing primary keys.
		- Example:
			`CREATE INDEX idx_users_email ON users(email);`
			- This creates an index on the email column, speeding up lookups for queries like:
			`SELECT * FROM users WHERE email = 'john@example.com';`
			- Instead of scanning the entire users table, the database can go directly to the indexed value.

	5. **Hash Indexes**:
		- Used for equality-based searches (`=`, but not `>`, `<`, `BETWEEN`).
		- Faster than B-Trees for exact matches.
		- Not useful for range queries or sorting.
		- Available in PostgreSQL and some MySQL storage engines.
		- Example:
			`CREATE INDEX idx_users_id_hash ON users(id) USING HASH;`
			- Speeds up queries like:
			`SELECT * FROM users WHERE id = 123;`

	6. **Full-Text Indexes**:
		- Optimized for searching text data, particularly for full-text search.
		- Supports natural language searching, phrase matching, and ranking results by relevance.
		- Available in MySQL (`FULLTEXT`), PostgreSQL (`GIN/GiST`), Oracle.
		- Example:
			`CREATE FULLTEXT INDEX idx_users_name ON users(name);`
			- Enables queries like:
			`SELECT * FROM users WHERE MATCH(name) AGAINST ('John Doe');`

	7. **GIN & GiST Indexes** (PostgreSQL-specific):
		- GIN (Generalized Inverted Index): Best for JSONB, full-text search, and array columns.
		- GiST (Generalized Search Tree): Used for geospatial and full-text search.
		- Example:
			`CREATE INDEX idx_users_data ON users USING GIN(data);`
			- Optimizes queries involving JSONB fields.

### 3.2. Query Optimization Techniques
1. Use `EXPLAIN ANALYZE`:
	- `EXPLAIN` shows how a query will be executed.
	- `ANALYZE` runs the query and provides execution statistics.
	- This reveals whether the database is using an index scan or full table scan.
2. Avoid `SELECT *`:
	- Fetching unnecessary columns increases memory usage and slows down queries.
	- Instead, only select the needed columns.
3. Optimize Joins:
	- Ensure foreign keys are indexed.
	- Use JOINs efficiently to avoid unnecessary computation.


## 4. Database Transactions & Concurrency Control
- ==A transaction== is a sequence of operations performed as a single unit of work. It ensures data consistency even when multiple users are interacting with the database.
- Transaction problems and isolation levels

| Isolation level  | Dirty reads | Non-repeatable reads | Phantom reads |
| ---------------- | ----------- | -------------------- | ------------- |
| Read uncommitted | ✔           | ✔                    | ✔             |
| Read committed   | ❌           | ✔                    | ✔             |
| Repeatable read  | ❌           | ❌                    | ✔             |
| Serializable     | ❌           | ❌                    | ❌             |
- Explanation:
	1. **Dirty Read** - A dirty read occurs when one transaction reads data that another transaction has not yet committed. If the second transaction rolls back, the first transaction read incorrect data.
	2. **Non-repeatable Read** - A non-repeatable read happens when a row is read twice in the same transaction, but another transaction modifies it in between.
	3. **Phantom Read** - A phantom read occurs when a transaction reads a set of rows twice, but a new row is inserted or deleted in between.


## 5. High availability & replication
- High availability (HA) ensures that a database remains accessible even if part of the system fails. One of the primary ways to achieve HA is replication, which means keeping multiple copies of the same database in different locations. Replication strategies help in load balancing, fault tolerance, and disaster recovery. There's three main types of replication strategies:
	1. **Master-Slave Replication (Primary-Replica Replication)** - A single master (primary) database handles all writes (`INSERT`, `UPDATE`, `DELETE`). One or more replica (slave) databases receive updates from the master but are read-only. Clients read from replicas to reduce the master’s load.
		- An example use case for this would be a read-heavy application like a news website or e-commerce platform. The Master handles transactions (purchases, account updates). Slaves handle read operations (product searches, recommendations).
		- The advantages of this approach are:
			- Read scaling: Offloads read queries from the master.
			- Failover: If the master fails, a replica can be promoted to master.
			- Simple setup: Easy to implement in MySQL, PostgreSQL, MongoDB, etc.
		- The disadvantages are:
			- Write bottleneck: The master handles all writes.
			- Replication lag: Replicas might lag behind the master.
			- No automatic failover: You must manually switch to a replica if the master crashes.

	2. **Master-Master Replication (Active-Active Replication)** - Multiple master nodes handle both reads and writes. Each master syncs changes with the other. If one master fails, another takes over without downtime.
		-  An example use case for this would be a High-Availability System like ATM networks, stock trading platforms (low-latency transactions) or multi-region applications (Amazon, Netflix, etc.)
		- The advantages are:
			- No single point of failure: If one master goes down, the system keeps running.
			- Write scalability: Multiple nodes handle writes.
			- Faster disaster recovery: Automatic failover to another master.
		- The disadvantages are:
			- Data conflicts: If the same row is updated by two masters simultaneously, conflicts can occur.
			- More complex to manage: Requires conflict resolution mechanisms.

	3. **Sharding (Partitioning Data Across Multiple Databases)** - Data is split across multiple independent databases (shards). Each shard contains only a portion of the data. A shard key determines which database a query goes to.
		- This approach is most useful when handling massive datasets. Examples from the real world include - Twitter, which stores tweets in different shards based on user ID; Facebook - stores user profiles across multiple databases; E-commerce platforms -Separate orders into regional shards.
		- The advantages are:
			- Massive scalability: You can add more shards as needed.
			- Avoids write bottlenecks: Each shard handles fewer writes.
			- Better performance for queries: Queries only scan a fraction of data.
		- The disadvantages are:
			- Increased complexity: Queries must know which shard to access.
			- Cross-shard joins are difficult: You can’t easily JOIN data across shards.
			- Rebalancing shards is hard: If a shard gets too big, rebalancing is tricky.

## 6. Database scaling and security best practices
### 6.1. Scaling strategies
- Scaling databases involves handling increased traffic without downtime. There are two main approaches:
	1. **Vertical Scaling** (Scaling Up) - Add more resources to a single server (CPU, RAM, SSD). The easiest way to scale initially. Simple to implement and no code changes needed, but limited by hardware (cannot keep upgrading forever) and also expensive (better hardware is costly).
	2. **Horizontal Scaling (Scaling Out)** - Distribute the database across multiple servers. Used in sharding, master-slave replication, and distributed databases. Scales infinitely and provides better fault tolerance (one machine failure doesn’t kill the system). Disadvantages are the increased complexity (must handle data distribution) and cross-server joins are harder. 
		- MongoDB, Cassandra, and Amazon DynamoDB are built for horizontal scaling. PostgreSQL with Citus turns a single-node database into a distributed system.

### 6.2. Security best practices
1. Use Least Privilege Access (`GRANT`) - Never give users more access than they need. Separate roles for admin, application users, and read-only users.
2. Encrypt Sensitive Data - Use AES for symmetric encryption and hashing for passwords. Never store raw passwords.
3. Enable SSL/TLS - Encrypts database connections. Prevents MITM (man-in-the-middle) attacks.
4. Perform Regular Backups - Use `mysqldump`, `pg_dump`, or cloud backups. Automate daily backups.


## 7. Schema design
- Schema design is fundamental for database performance, maintainability, and scalability. A well-structured schema can improve query performance, ensure data integrity, and optimize storage efficiency. 
### 7.1. Normalization
- Normalization is the process of structuring a relational database to reduce redundancy and improve data integrity. It involves decomposing large tables into smaller, related tables while maintaining relationships through foreign keys.
- There are several normalization forms:
	1. **First Normal Form (1NF)** - Ensures atomicity: each column contains only atomic (indivisible) values. Ensures uniqueness: each row must have a unique identifier (Primary Key). Eliminates duplicate rows and multi-valued attributes.
		- Bad design example:
			`CREATE TABLE Students (`
		    `student_id INT PRIMARY KEY,`
		    `name VARCHAR(100),`
		    `subjects TEXT -- Storing multiple subjects in a single column is bad practice`
			`);`
		- Good design example:
			`CREATE TABLE Students (`
		    `student_id INT PRIMARY KEY,`
		    `name VARCHAR(100)`
			`);`

			`CREATE TABLE StudentSubjects (`
		    `student_id INT,`
		    `subject VARCHAR(100),`
		    `FOREIGN KEY (student_id) REFERENCES Students(student_id)`
			`);`

	2. **Second Normal Form (2NF)** - Must be in 1NF. Removes partial dependencies (where non-key attributes depend on only part of a composite primary key).
		- Bad design:
			`CREATE TABLE Orders (`
		    `order_id INT,`
		    `product_id INT,`
		    `product_name VARCHAR(100), -- product_name depends only on product_id, not order_id`
		    `PRIMARY KEY (order_id, product_id)`
			`);`
		- Good design:
			`CREATE TABLE Orders (`
		    `order_id INT PRIMARY KEY`
			`);`

			`CREATE TABLE Products (`
		    `product_id INT PRIMARY KEY,`
		    `product_name VARCHAR(100)`
			`);`

			`CREATE TABLE OrderDetails (`
		    `order_id INT,`
		    `product_id INT,`
		    `FOREIGN KEY (order_id) REFERENCES Orders(order_id),`
		    `FOREIGN KEY (product_id) REFERENCES Products(product_id)`
			`);`
	3. **Third Normal Form (3NF)** - Must be in 2NF. Removes transitive dependencies (where a non-key attribute depends on another non-key attribute).
		- Bad design:
			`CREATE TABLE Employees (`
		    `emp_id INT PRIMARY KEY,`
		    `emp_name VARCHAR(100),`
		    `dept_id INT,`
		    `dept_name VARCHAR(100) -- dept_name depends on dept_id, not emp_id`
			`);`
		- Good design:
			`CREATE TABLE Departments (`
		    `dept_id INT PRIMARY KEY,`
		    `dept_name VARCHAR(100)`
			`);`

			`CREATE TABLE Employees (`
		    `emp_id INT PRIMARY KEY,`
		    `emp_name VARCHAR(100),`
		    `dept_id INT,`
		    `FOREIGN KEY (dept_id) REFERENCES Departments(dept_id)`
			`);`

	4. **Boyce-Codd Normal Form (BCNF)** - Ensures that every determinant is a candidate key. A stronger version of 3NF that removes anomalies caused by multiple overlapping candidate keys.

### 7.2. Denormalization
- Denormalization is the process of introducing redundancy to improve read performance by reducing the need for complex joins.
- Denormalization provides faster read queries (due to fewer joins) but the trade-off comes in the shape of higher storage cost and increased complexity when updating data (redundancy).
- An example of denormalization would be that instead of keeping `customer_name` in a separate `Customers` table, we might store it directly in the `Orders` table for faster queries. 

### 7.3. Partitioning
- Partitioning divides a large table into smaller, more manageable physical sections while keeping them logically as a single table.
- Partitioning improves query performance (by scanning only relevant partitions), enhances manageability (e.g., deleting old data efficiently), and helps with scaling by distributing data across disks/servers.
- There are several main types of partitioning:
	1.  **Range Partitioning**: Divides data based on ranges of values in a column. Useful for time-series data (e.g., logs, orders).
		- Example use case - splitting orders by yearly ranges (e.g., 2023, 2024):
			`CREATE TABLE Orders (`
		    `order_id SERIAL,`
		    `order_date DATE NOT NULL`
			`) PARTITION BY RANGE (order_date);`

	2. **List Partitioning**: Divides data based on specific values in a column. Useful when dealing with categorical data (e.g., country, department).
		 - Example use case - separating employees based on department:
			`CREATE TABLE Employees (`
		    `emp_id INT,`
		    `department VARCHAR(50)`
			`) PARTITION BY LIST (department);`

	3. **Hash Partitioning**: Uses a hash function to distribute data evenly. Useful for load balancing when data distribution is unknown.
		- Example use case - ensuring even distribution of customers across partitions:
			`CREATE TABLE Customers (`
		    `customer_id INT`
			`) PARTITION BY HASH (customer_id);`

	4. **Composite Partitioning**: Combines multiple partitioning methods (e.g., Range + Hash).
		- Example use case - partitioning sales by date, then further subpartitioning by region:
			`CREATE TABLE Sales (`
		    `sale_id SERIAL,`
		    `sale_date DATE NOT NULL,`
		    `region VARCHAR(50)`
			`) PARTITION BY RANGE (sale_date) SUBPARTITION BY LIST (region);`