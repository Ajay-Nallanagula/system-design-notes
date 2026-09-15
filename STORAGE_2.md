STORAGE , for System Design Notes

- [Databases - SQL, NoSQL (key-value, document, columnar, graph), NewSQL](#databases)
- [Object Storage - Amazon S3, Google Cloud Storage, Azure Blob Storage](#object-storage)
- [Block Storage - Network-attached storage (NAS), storage area networks (SAN)](#block-storage)
- [File Systems - Distributed file systems (HDFS, Ceph), Network File System (NFS)](#file-systems)
- [Caching - Redis, Memcached, Varnish, CDN edge caches](#caching)

### Databases

- [Databases](https://youtu.be/O_c7lzNbcKo?si=j5NzoNUyT4MPMS1m)
- \*\*\* [DB's Comparision](https://youtu.be/VfcRxtBKI54?si=gKJ78QGs9A9La94b)

- different types of data, different volume of data define which database to use.
- As a metaphor, a room in hospital is different from a room in a hotel and a theater is different from both. All are rooms, but they serve different purposes and have different requirements. Similarly, different databases are designed to handle different types of data and workloads.

- There are multiple types of databases, each optimized for specific use cases:
  - `SQL Databases`: `Relational databases` that use structured query language (SQL) for defining and manipulating data. Examples include MSSQL, MySQL, PostgreSQL, and Oracle.
    - **Example**: E-commerce system with Users, Products, and Orders tables linked via Foreign Keys. ACID compliant, strong consistency.

    ```sql
    CREATE TABLE Users (user_id INT PRIMARY KEY, name VARCHAR(100), email VARCHAR(100) UNIQUE);
    CREATE TABLE Orders (order_id INT PRIMARY KEY, user_id INT, order_date TIMESTAMP, FOREIGN KEY (user_id) REFERENCES Users(user_id));
    ```

  - `NoSQL Databases`: `Non-relational databases` that can handle unstructured data and are designed for scalability. Types include:
    - `Key-Value Stores`: Store data as key-value pairs (e.g., Redis, DynamoDB).
      Client side, localStorage , sessionStorage are Key-Value stores in the browser.
      - **Example**: User session caching with Redis

      ```
      SET user:1001:session "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
      SET user:1002:session "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
      GET user:1001:session  # Returns the session token instantly
      EXPIRE user:1001:session 3600  # Auto-expire after 1 hour
      ```

      Fast in-memory access, perfect for caching and real-time data.

    - `Document Stores`: Store data in document format (e.g., MongoDB, CouchDB).
    - No fixed Schema, not sure how schema is going to evolve over time, flexible data models, ideal for hierarchical data and evolving schemas.
    - Supports heavy reads and writes, flexible data models, ideal for hierarchical data and evolving schemas.
    - Don't support ACID transactions
      - **Example**: User profiles in MongoDB

      ```json
      db.users.insertOne({
        _id: ObjectId("123456789"),
        username: "john_doe",
        email: "john@example.com",
        profile: {
          age: 28,
          location: "USA",
          interests: ["coding", "gaming", "music"]
        },
        orders: [
          { order_id: "ORD001", total: 99.99, date: ISODate("2024-01-15") },
          { order_id: "ORD002", total: 149.99, date: ISODate("2024-02-20") }
        ]
      })
      ```

      Flexible schema, nested data, perfect for hierarchical/complex objects. Easy to store related data together.

    - `Columnar Databases`: Store data in columns rather than rows (e.g., Cassandra, HBase).
    - Don't support ACID transactions
    - **Example**: Time-series metrics in Cassandra, music, video streaming, IoT data
    - Your columns are designed keeping your queries in mind
      - Example "User-liked-songs" , "User-liked-videos" , "User-liked-posts" are different tables in Cassandra, but they are all designed to answer the same question "What content does a user like?" and they are optimized for that query.

    ```cql
    CREATE TABLE metrics (
      server_id UUID,
      timestamp TIMESTAMP,
      cpu_usage DECIMAL,
      memory_usage DECIMAL,
      disk_usage DECIMAL,
      PRIMARY KEY (server_id, timestamp)
    ) WITH CLUSTERING ORDER BY (timestamp DESC);

    INSERT INTO metrics JSON '{"server_id": "550e8400-e29b-41d4-a716-446655440000",
                              "timestamp": "2024-01-15T10:30:00Z",
                              "cpu_usage": 45.2,
                              "memory_usage": 78.5,
                              "disk_usage": 62.1}';
    ```

    Optimized for analytics queries on specific columns, compresses well, fast aggregations. Ideal for time-series data and OLAP workloads.
    - `Graph Databases`: Store data in graph structures with nodes and edges (e.g., Neo4j, Amazon Neptune).
      - **Example**: Social network relationships in Neo4j

      ```cypher
      CREATE (user1:User {id: "U001", name: "Alice", email: "alice@example.com"})
      CREATE (user2:User {id: "U002", name: "Bob", email: "bob@example.com"})
      CREATE (user3:User {id: "U003", name: "Charlie", email: "charlie@example.com"})

      CREATE (user1)-[:FRIENDS_WITH]->(user2)
      CREATE (user2)-[:FRIENDS_WITH]->(user3)
      CREATE (user1)-[:FOLLOWS]->(user3)

      MATCH (alice:User {name: "Alice"})-[:FRIENDS_WITH*1..2]-(friend)
      RETURN friend
      ```

      Perfect for relationships, recommendations, social networks, and traversing connections. Fast path queries, excellent for connected data.

  - `Search DB` : Optimized for full-text search and complex queries (e.g., Elasticsearch, Solr).
    - **Example**: Product search in Elasticsearch, Search will always be a secondary DB, the list of products will be stored in a primary DB (SQL or NoSQL) and the search index will be built from that data and stored in Elasticsearch, the search index will be optimized for search queries and relevance scoring.

    ```json
    POST /products/_doc/1
    {
      "name": "Wireless Headphones",
      "description": "High-quality wireless headphones with noise cancellation.",
      "price": 199.99,
      "category": "Electronics"
    }

    GET /products/_search
    {
      "query": {
        "match": {
          "description": "wireless headphones"
        }
      }
    }
    ```

    Optimized for text search, relevance scoring, and complex queries. Ideal for e-commerce product search, log analytics, and any scenario requiring powerful search capabilities.

- `Vector Databases`: Specialized databases designed to store and query high-dimensional vector data, often used in machine learning and AI applications (e.g., Pinecone, Weaviate).
  Example: Storing and querying image embeddings for similarity search. Image searches
- PDF document searxches, code searches, video searches, audio searches, any unstructured data that can be converted into vector embeddings can be stored and queried in a vector database.
  - `NewSQL Databases`: `Relational databases` that provide the scalability of NoSQL while maintaining ACID properties (e.g

DB terminology Practices:
https://youtu.be/TUcPS6dsWx4?si=RG4IYarCEodtv3lk
[SHARDING](https://youtu.be/L521gizea4s?si=pieiDViVLAOsOela)

### Object Storage

[Object Storage](https://youtu.be/RvaMHMxHjp4?si=eVdVZR9UDyZfYasE)

`What is Object Storage?`

- Object storage is a type of data storage architecture that manages data as objects, rather than as files within a hierarchical file system (as in traditional file storage) or as blocks within sectors and tracks (as in block storage). Each object typically includes the data itself, metadata, and a unique identifier
- Object storage is designed to handle large amounts of unstructured data, such as multimedia files, backups, and big data. It is highly scalable, durable, and accessible over the internet, making it ideal for cloud storage solutions.
- Object storage is often used for storing and managing large volumes of data that do not require the low-latency access provided by block storage, such as images, videos, and documents. It is commonly used in cloud storage services like `Amazon S3`, `Google Cloud Storage`, and `Azure Blob Storage`.

`Why low-latency doesn't matter:`
These use cases typically don't need millisecond-level response times. A user waiting for a video to download or an archive to be retrieved doesn't require the same speed as a database query. This trade-off allows object storage to be much cheaper and more scalable

`Advantages of Object Storage:`

- Scalability: Object storage can scale to handle petabytes of data across distributed clusters without performance degradation.
- Durability: Object storage systems often replicate data across multiple nodes and locations, providing high durability and fault tolerance.
- Accessibility: Object storage is accessible over the internet using APIs, making it

`Use case scenarios where we use Object Storage`

- Storing and serving large media files (e.g., images, videos, audio).
- Backup and archival storage for long-term data retention.
- Storing unstructured data for big data analytics.
- Hosting static websites and content delivery.
- Storing and managing large datasets for machine learning and AI applications.
- Storing and managing user-generated content in social media platforms.
- Storing and managing logs and event data for monitoring and analysis.

`What is disadvantage of storing BLOB in database over Object Storage?`
Storing BLOBs (Binary Large Objects) in a database can lead to several disadvantages compared to using object storage:

- Performance: Databases are optimized for structured data and may not perform well when handling large BLOBs, leading to slower read/write operations.
- Scalability: Databases may struggle to scale effectively when storing large BLOBs, while object storage is designed to handle massive amounts of unstructured data.
- Cost: Storing large BLOBs in a database can be more expensive due to increased storage requirements and potential performance issues, whereas object storage is typically more cost-effective for large unstructured data.
- Backup and Recovery: Backing up and recovering databases with large BLOBs can be time-consuming and resource-intensive,

`Object Storage` has flat heirarchy

- Flat namespace, no hierarchy, no folders, just a flat structure where each object is identified by a unique key. This allows for massive scalability and flexibility in how data is stored and accessed.
- The folders that you create in Amazon S3 or Google Cloud Storage are just a visual representation of the key structure, they don't actually exist as separate entities in the storage system. The object storage system treats all objects as part of a single flat namespace, and the keys are used to organize and access the data.

Typically Object storage is used along with Databases, example lets say userprofile , the user info is stored in a SQL or NoSQL database, but the profile picture of the user is stored in object storage, and the database will store the URL or reference to the object in the object storage.

`The image to Object storage can be uploaded in the following way:`

1. The upload using presigned URL, the client can directly upload the image to the object storage without going through the application server, this reduces the load on the application server and allows for faster uploads.
2. The upload using the application server, the client uploads the image to the application server, and then the application server uploads the image to the object storage, this allows for more control over the upload process, such as validating the image, resizing it, or adding metadata before uploading it to the object storage.
3. The upload using multipart upload, for large files, the client can upload the file in multiple parts, this allows for faster uploads and also allows for resuming uploads if they fail.
4. using chunked upload, the client can upload the file in chunks, this allows for faster uploads and also allows for resuming uploads if they fail.

`What are presigned URLs?`
Presigned URLs are a feature of object storage services that allow you to generate a temporary URL that grants access to a specific object in the storage for a limited time. This is particularly useful for allowing clients to upload or download files directly to/from the object storage without needing to go through your application server, which can help reduce server load and improve performance. When you generate a presigned URL, you specify the object you want to access, the HTTP method (GET for downloading, PUT for uploading), and the expiration time for the URL.

System Design for S3
https://youtu.be/chG5HV-mGa0?si=ejZKtLKefUD1aoDh

### Block Storage

`What it is:`

Data divided into fixed-size blocks (typically 4KB-64KB each)
Each block has a unique address, allowing random access to any block
Acts as the foundation for traditional storage systems
Key characteristics:

`Low-latency access` — Direct access to specific blocks without reading entire files
`Suitable for` — Databases, operating systems, transaction logs, virtual machines
`High performance` — Optimized for frequent, repeated reads/writes to specific locations
`Predictable behavior` — ACID compliance and strong consistency
`Common implementations`:

`Network-attached storage (NAS)` — Shared storage over Ethernet for multiple users/servers
`Storage area networks (SAN)` — Dedicated high-speed networks connecting servers to storage
`Cloud block storage` — AWS EBS (Elastic Block Store), Google Persistent Disks, Azure Managed Disks
`How it differs from object storage`:

| Aspect         | Block Storage       | Object Storage            |
| -------------- | ------------------- | ------------------------- |
| Access Method  | Direct block access | Access via HTTP APIs      |
| Latency        | Low-latency         | Higher latency acceptable |
| Use Cases      | Databases, VMs      | Images, videos, backups   |
| Cost           | More expensive      | Cost-effective            |
| Data Structure | Fixed size blocks   | Variable-size objects     |

Use cases:
Running databases (MySQL, PostgreSQL, MongoDB)
Virtual machine disks
Transaction processing systems
Applications needing real-time data access
Bottom line: Block storage is for performance-critical, transactional workloads where you need instant access to specific data, while object storage is for large-scale, infrequent-access data.

### File Systems

`What are File Systems?`

- File systems organize and manage data as hierarchical structures of files and directories, providing an interface for storing and retrieving files from storage devices.
- They handle data organization, access control, and retrieval across single or multiple computers.

`Types of File Systems:`

- `Distributed File Systems` — Designed to store and manage files across multiple nodes/servers:
  - **HDFS (Hadoop Distributed File System)** — Optimized for big data processing, stores large files across a cluster, fault-tolerant through replication
  - **Ceph** — Highly scalable, self-healing distributed storage system supporting objects, blocks, and files
  - Ideal for: Large-scale data analytics, big data frameworks (Hadoop, Spark)

- `Network File System (NFS)` — Allows computers to access files over a network as if they were on local storage:
  - Shared file access across multiple clients and servers
  - Traditional approach, works well for small to medium enterprises
  - Ideal for: Shared storage, collaborative environments, NAS systems

| Characteristic      | Distributed FS       | Network File System      |
| ------------------- | -------------------- | ------------------------ |
| **Scalability**     | Massive (petabytes)  | Limited                  |
| **Fault Tolerance** | Built-in replication | Requires separate backup |
| **Use Case**        | Big data, analytics  | General file sharing     |
| **Examples**        | HDFS, Ceph           | NFS, SMB                 |

`Key Differences from Object Storage:`

- File systems maintain **hierarchical structure** (folders/directories) while object storage is flat
- File systems are optimized for **general-purpose file access**, object storage for **large unstructured data**
- File systems work well with traditional applications, object storage for cloud-native systems

### Caching
https://youtu.be/1NngTUYPdpI?si=E03CviFhE8qC6C-4

`What is caching?`
- Caching is a technique used to store frequently accessed data in a temporary storage area (cache) to  reduce latency and improve performance.
- Caches can be implemented at various levels, including hardware (CPU cache), software (in-memory caches, Application level), External Cache and network (CDN edge caches).

`Types of Caches:`
- `In-memory Caches` — Store data in RAM for ultra-fast access:
  - **Redis** — In-memory key-value store with support for data structures, pub/sub, and persistence
  - **Memcached** — Simple in-memory key-value store for caching small chunks of data
  - Ideal for: Session storage, real-time analytics, caching database query results
- `CDN Edge Caches` — Cache content at the edge of the network to reduce latency for end-users:
- Client Cache — Caches data on the client side (browser cache, mobile app cache) to reduce server requests and improve responsiveness.

`Cache Architecture Patterns:`
- `Cache-aside (Lazy Loading)` — Application checks cache first, if miss, loads from database and populates cache popular, often used , default to be used
- `Write-through` — Writes(POST requests) go through cache to database, ensures cache is always up-to-date, but can add latency to writes.
What if cache-write is saved and DB write fails?
- `Write-back` — Writes go to cache first, then asynchronously written to database, improves write performance but risks data loss if cache fails before DB write.
What if cache-write is saved and DB write fails?
- `Read-through` — Reads go through cache, if miss, cache loads from database and returns data, simplifies application logic but can add latency on cache misses.

`Cache Eviction Policies:`
- `LRU (Least Recently Used)` — Evicts the least recently accessed items first, good for workloads with temporal locality.
- `LFU (Least Frequently Used)` — Evicts items that are accessed least frequently, good for workloads with stable access patterns.
- `FIFO (First In First Out)` — Evicts items in the order they were added, simple but can lead to suboptimal cache utilization.
- `Random` — Evicts random items, simple but can lead to unpredictable performance.
- `TTL (Time To Live)` — Evicts items after a certain time period, good for data that becomes stale over time.

