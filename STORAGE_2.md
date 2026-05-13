STORAGE , for System Design Notes

- [Databases - SQL, NoSQL (key-value, document, columnar, graph), NewSQL](#databases)
- [Object Storage - Amazon S3, Google Cloud Storage, Azure Blob Storage](#object-storage)
- [Block Storage - Network-attached storage (NAS), storage area networks (SAN)](#block-storage)
- [File Systems - Distributed file systems (HDFS, Ceph), Network File System (NFS)](#file-systems)
- [Caching - Redis, Memcached, Varnish, CDN edge caches](#caching)

### Databases

- [Databases](https://youtu.be/O_c7lzNbcKo?si=j5NzoNUyT4MPMS1m)
- *** [DB's Comparision](https://youtu.be/VfcRxtBKI54?si=gKJ78QGs9A9La94b) 

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

### Object Storage

https://youtu.be/RvaMHMxHjp4?si=eVdVZR9UDyZfYasE

System Design for S3
https://youtu.be/chG5HV-mGa0?si=ejZKtLKefUD1aoDh

### Block Storage

### File Systems

### Caching
