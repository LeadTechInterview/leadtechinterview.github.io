## 1. **Database Selection by QPS (Read vs Write)**

| **QPS Range**         | **Type of Database**                  | **Examples**                      | **Scaling Techniques**             |
|-----------------------|---------------------------------------|-----------------------------------|------------------------------------|
| **Low (<1,000)**      | Relational / Document DB              | MySQL, PostgreSQL, MongoDB        | Vertical scaling, Caching         |
| **Medium (1k–10k)**   | Relational (tuned) / Document DB      | MySQL + Replicas, DynamoDB        | Read replicas, Caching, Indexing  |
| **High (10k–1M)**     | Distributed NoSQL                     | Cassandra, DynamoDB, Bigtable     | Sharding, Horizontal scaling      |
| **Extreme (>1M)**     | Distributed In-memory / Advanced DB   | Redis, CockroachDB, Spanner       | Sharding, Partitioning, CDNs      |

---

## 2. **Mainstream Database QPS Capacity (Read vs Write)**

| **Database Type**      | **Database**            | **Read QPS (Per Node)**            | **Write QPS (Per Node)**           |
|------------------------|-------------------------|------------------------------------|------------------------------------|
| **Relational DB**       | MySQL, PostgreSQL       | 2k–10k QPS (optimized for reads)  | 500–2k QPS (write-optimized)      |
| **Document DB**         | MongoDB                 | 5k–20k QPS (tuned for reads)      | 1k–5k QPS (write-heavy)           |
| **Wide-Column Store**   | Cassandra               | 10k–50k QPS (cluster optimized)   | 5k–20k QPS (write-optimized)      |
| **Key-Value Store**     | Redis                   | 100k–1M QPS (in-memory optimized) | 100k–1M QPS (write-intensive)     |
| **Time-Series DB**      | InfluxDB                | 50k–500k QPS                      | 10k–50k QPS                       |
| **Distributed SQL**     | CockroachDB             | 10k–50k QPS                       | 2k–10k QPS                        |
| **Search Engines**      | Elasticsearch           | 1k–20k QPS (query-dependent)      | 500–5k QPS (write-intense)        |

---

## 3. **Sharding and Scaling: When and Why**

### **Key Indicators for Sharding**:

| **Condition**              | **Indicators**                                                             | **Action**                               |
|----------------------------|---------------------------------------------------------------------------|------------------------------------------|
| **Data Volume**             | Storage exceeds capacity of a single node or disk.                        | Use **sharding** when storage exceeds ~500GB–1TB per node. |
| **High Write Throughput**   | Write latency increases due to bottlenecks.                               | Shard by write-heavy keys (>5k–10k writes/sec). |
| **Read/Write Latency**      | Latency exceeds acceptable thresholds.                                   | Partition data, shard for high loads.    |
| **Data Hotspotting**        | Some partitions/shards receive disproportionate traffic.                 | Implement **sharding** to balance load across nodes. |
| **Query Performance**       | Queries are slow due to large tables.                                     | Use **partitioning** to improve query performance. |

---

## 4. **Other Scaling Concepts to Consider**

### **1. Load Balancing**

| **Concept**               | **Description**                                                          | **Techniques**                      |
|---------------------------|--------------------------------------------------------------------------|-------------------------------------|
| **Load Balancing**         | Distribute incoming traffic across multiple servers to ensure reliability and prevent overload. | **Round-robin**, **Least Connections**, **Weighted balancing** using **NGINX**, **HAProxy**, **AWS ELB**. |

### **2.  Fault Tolerance, CAP Theorem**

| **Concept**               | **Description**                                                          | **Techniques**                      |
|---------------------------|--------------------------------------------------------------------------|-------------------------------------|
| **Fault Tolerance**        | Ensuring that the system remains operational even if some components fail. | **Replication**, **Consensus algorithms** (e.g., **Paxos**, **Raft**), **Failover** systems. |
| **CAP Theorem**            | A distributed system can only guarantee **two** of the following: **Consistency**, **Availability**, or **Partition Tolerance**. | Choose between **eventual consistency** or **strong consistency** depending on system requirements. |
| **Eventual Consistency**   | Allowing data to propagate slowly across nodes, often used in **NoSQL** databases. | Use **CRDTs**, **Event sourcing**, **CQRS**, **Tunable consistency**. |
| **Data Replication**       | Duplication of data across nodes to ensure high availability and fault tolerance. | Use **Master-Slave replication**, **Multi-region replication**. |

### **3. Queues and Asynchronous Processing**

| **Concept**               | **Description**                                                          | **Techniques**                      |
|---------------------------|--------------------------------------------------------------------------|-------------------------------------|
| **Asynchronous Processing** | Offloading long-running tasks to background jobs for better system responsiveness. | **Message queues** like **RabbitMQ**, **Kafka**, **Amazon SQS**, **Job schedulers**. |
| **Event-driven Architectures** | Architecting systems to react to events in real-time. | Use **Event-driven systems** with **publish-subscribe** models. |

### **4. Auto-Scaling and Elastic Infrastructure**

### **5. Global Distribution & Multi-Region Deployment**
