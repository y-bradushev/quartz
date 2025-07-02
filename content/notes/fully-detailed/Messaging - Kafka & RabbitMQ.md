## 1. Overview
- Event-driven architecture (EDA) is a design pattern where components of a system communicate by sending events to one another. Events are typically messages that carry information about a state change or some significant occurrence in the system.
- In EDA, event producers generate messages, and event consumers process them asynchronously. This decouples components, allowing for better scalability and fault tolerance. Two popular messaging systems for building such architectures are Apache Kafka and RabbitMQ.

## 2. Apache Kafka
- Apache Kafka is a distributed, highly scalable event streaming platform designed to handle high throughput, low latency, and fault tolerance. It is used to build real-time data pipelines and streaming applications. Kafka is based on the publish-subscribe model and is ideal for applications requiring high concurrency and fault tolerance.

### 2.1. Key components
1. **Distributed Architecture**
	- Kafka is inherently a distributed system. It operates as a cluster of brokers, each of which handles part of the data and workload. This architecture ensures horizontal scalability by allowing additional brokers to be added to the cluster without disrupting the existing setup.
	- Kafka brokers are responsible for managing partitions of topics and handling reads/writes for consumers and producers. Typically, Kafka clusters consist of multiple brokers to ensure high availability and fault tolerance.
	- Kafka uses Zookeeper to manage the cluster metadata, including leader election and coordination of brokers.
2. **Topics and Partitions**
	- Kafka organizes events into topics. A topic is a category to which messages are sent by producers. Topics allow logical segregation of different event types (e.g., “user-activity” or “payment-events”).
	- Each topic is divided into one or more partitions, enabling parallelism and scalability. Partitions are the key unit of parallelism and distribution. Each partition is an ordered, immutable sequence of messages, which makes Kafka ideal for handling large volumes of data with low latency.
	- Kafka guarantees message order within a partition, but not across partitions. If event order is critical, it’s important to design your partitioning strategy carefully.
3. **Consumer groups**
	- Kafka allows consumers to group into consumer groups, which allows for load balancing of the consumer workload across multiple consumers.
	- Each consumer within a group is assigned one or more partitions to consume messages from, ensuring that each message is consumed by exactly one consumer in the group.
	- Kafka consumers can scale horizontally by adding more consumers to a consumer group. The partition count limits the parallelism of consumer groups, so if there are fewer consumers than partitions, some consumers may be idle.
4. **Durability and Fault Tolerance**
	- Kafka provides strong durability guarantees by writing messages to disk and replicating partitions across multiple brokers. This ensures that even if a broker crashes, the data is not lost.
	- Each partition can be replicated across multiple brokers. If one broker goes down, another broker with a replica of the partition can take over. The replication factor (number of copies) is configurable.
5. **Stream Processing**
	- Kafka provides a native stream processing library called Kafka Streams, which allows for real-time data processing directly within the Kafka ecosystem. It supports operations such as filtering, aggregation, and joining of streams, and can be used for building real-time data pipelines and analytics applications.
	- KSQL is an SQL-like query engine for stream processing in Kafka. It allows users to query streams and perform operations like joins, aggregates, and filters on streaming data.