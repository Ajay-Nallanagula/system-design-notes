# SYSTEM DESIGN NOTES

## TOPICS

[System Design Primer](https://www.youtube.com/watch?v=FSR1s2b-l_I&list=PLTCrU9sGyburBw9wNOHebv9SjlE4Elv5a)

𝐒𝐲𝐬𝐭𝐞𝐦 𝐃𝐞𝐬𝐢𝐠𝐧 𝐂𝐨𝐦𝐩𝐨𝐧𝐞𝐧𝐭𝐬

📌 𝐍𝐞𝐭𝐰𝐨𝐫𝐤𝐢𝐧𝐠

- DNS - Domain Name System (resolvers, nameservers, records)
- Load Balancers - Hardware, software, Layer 4, Layer 7
- CDNs - Content Delivery Networks (caching, edge servers)
- Proxies - Forward, reverse, transparent, anonymous
- VPNs - Virtual Private Networks (tunneling protocols)
- Firewalls - Packet filtering, stateful inspection
- NAT - Network Address Translation
- Gateways - Connect different networks
- Routers - Direct traffic between networks
- See NETWORK_1.md for more details.

📌 𝐒𝐭𝐨𝐫𝐚𝐠𝐞

- Databases - SQL, NoSQL (key-value, document, columnar, graph), NewSQL
- Object Storage - Amazon S3, Google Cloud Storage, Azure Blob Storage
- Block Storage - Network-attached storage (NAS), storage area networks (SAN)
- File Systems - Distributed file systems (HDFS, Ceph), Network File System (NFS)
- Caching - Redis, Memcached, Varnish, CDN edge caches

📌 𝐂𝐨𝐦𝐩𝐮𝐭𝐞

- Servers - Bare metal, virtual machines (VMs)
- Containers - Docker, Kubernetes, container orchestration
- Serverless - AWS Lambda, Azure Functions, Google Cloud Functions
- FaaS - Function-as-a-Service
- PaaS - Platform-as-a-Service

📌 𝐂𝐨𝐦𝐦𝐮𝐧𝐢𝐜𝐚𝐭𝐢𝐨𝐧

- APIs - REST, GraphQL, SOAP, gRPC
- Message Queues - RabbitMQ, Kafka, ActiveMQ, Amazon SQS
- WebSockets - Real-time, full-duplex communication
- RPC - Remote Procedure Call, XML-RPC, JSON-RPC
- Pub/Sub - Publish-subscribe messaging pattern
- Service Mesh - Istio, Linkerd

📌 𝐀𝐫𝐜𝐡𝐢𝐭𝐞𝐜𝐭𝐮𝐫𝐚𝐥 𝐏𝐚𝐭𝐭𝐞𝐫𝐧𝐬

- Microservices - Domain-driven design (DDD), service discovery, API gateways
- Monolithic - Layered architecture, MVC, MVP
- Event-driven - Event sourcing, CQRS
- Microkernel or Plug-in Architecture
- DDD - Domain-Driven Design
- Serverless - FaaS, BaaS (Backend-as-a-Service)

📌 𝐒𝐜𝐚𝐥𝐚𝐛𝐢𝐥𝐢𝐭𝐲 & 𝐑𝐞𝐥𝐢𝐚𝐛𝐢𝐥𝐢𝐭𝐲

- Horizontal Scaling - Load balancers, auto-scaling groups
- Vertical Scaling - Larger instances, more resources
- Replication - Master-slave, master-master
- Sharding - Partitioning data across multiple databases
- Redundancy - Multiple instances, failover mechanisms
- Fault Tolerance - Graceful degradation, circuit breakers
- Disaster Recovery - Backups, replication, geo-redundancy

📌 𝐒𝐞𝐜𝐮𝐫𝐢𝐭𝐲

- Authentication - Multi-factor authentication (MFA), single - sign-on (SSO), OAuth, OpenID Connect
- Authorization - Role-based access control (RBAC), Attribute-based - access control
- Encryption - Symmetric, asymmetric, hashing algorithms
- Security Protocols - TLS/SSL, HTTPS, SSH
- Web Application Firewalls - Protect against web attacks
- Intrusion Detection Systems - Identify malicious activity

📌 𝐎𝐛𝐬𝐞𝐫𝐯𝐚𝐛𝐢𝐥𝐢𝐭𝐲

- Monitoring - Prometheus, Grafana, Datadog, New Relic
- Logging - ELK Stack (Elasticsearch, Logstash, Kibana), Splunk
- Tracing - Distributed tracing (Jaeger, Zipkin)
- Metrics - Counters, gauges, histograms, summaries

- APIs: https://lnkd.in/dsbwPZ6N
-
- API Gateway: https://lnkd.in/gtyXmvf4
-
- Scalability: https://lnkd.in/gPGhW-qK
-
- Availability: https://lnkd.in/gQk2p4_6
-
- CAP Theorem: https://lnkd.in/g_tFqJJb
-
- Load Balancers: https://lnkd.in/dzM7VDKq
-
- Load Balancing Algorithms: https://lnkd.in/gvxfwEUr
-
- Caching: https://lnkd.in/gBSeTstS
-
- Caching Strategies: https://lnkd.in/dVk7nZ_Y
-
- Cache Eviction Strategies: https://lnkd.in/gQAEXEmq
-
- Distributed Caching: https://lnkd.in/gf6pCqdi
-
- CDNs: https://lnkd.in/g2zvPhkA
-
- Database Types: https://lnkd.in/gDxtZVE2
-
- SQL vs NoSQL: https://lnkd.in/gHyC9qWc
-
- ACID Transactions: https://lnkd.in/dB3QHiMz
-
- Database Indexes: https://lnkd.in/g_-bQWtA
-
- Database Sharding: https://lnkd.in/g9mc-d5m
-
- Database Scaling: https://lnkd.in/gSUtAwc7
-
- WebSockets: https://lnkd.in/gD-TUZep
-
- Message Queues: https://Inkd.in/g-jnNGDC
-
- Service Discovery: https://lnkd.in/gRj_QeP8
-
- Bloom Filters: https://lnkd.in/gfGjCrSZ
-
- Rate Limiting Algorithms: https://lnkd.in/gYDxg8XY
-
- SPOF: https://lnkd.in/gw_uHZWn
-
- Idempotency: https://lnkd.in/gDB3A.Jij
-
- Checksums: https://lnkd.in/gGPEKV8b
-
- Long Polling vs WebSockets: https://lnkd.in/d9xKD28K
-
- Concurrency vs Parallelism: https://lnkd.in/gGZXhjBD
-
- Proxy vs Reverse Proxy: https://lnkd.in/gMTtidBq
-
- Batch vs Stream Processing: https://lnkd.in/gKtj_qWh
