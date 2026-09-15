- [APIs - REST, GraphQL, SOAP, gRPC](#apis)
- [Message Queues - RabbitMQ, Kafka, ActiveMQ, Amazon SQS](#message-queues)
- [WebSockets - Real-time, full-duplex communication](#websockets)
- [RPC - Remote Procedure Call, XML-RPC, JSON-RPC](#rpc)
- [Pub/Sub - Publish-subscribe messaging pattern](#pubsub)
- [Service Mesh - Istio, Linkerd](#service-mesh)

### APIs

#### 1. REST (Representational State Transfer)

**Problem Solved:** Simplifies client-server communication with stateless, standard HTTP methods, replacing complex RPC-based systems with a more intuitive resource-based approach.
**Use Cases:** Web APIs, mobile backends, microservices, public APIs (Twitter, GitHub), CRUD operations on resources, stateless server communication.

#### 2. SOAP (Simple Object Access Protocol)

**Problem Solved:** Provides strict standardization and complex operation support beyond simple CRUD, solving the need for enterprise-grade security, formal contracts, and transactions.
**Use Cases:** Legacy enterprise systems, banking/finance transactions, systems requiring WS-Security, formal WSDL contracts, strict error handling.

#### 3. gRPC (Google Remote Procedure Call)

`What is RPC? ` Remote Procedure Call (RPC) is a protocol that allows a program to execute a procedure (subroutine) on a different address space (commonly on another computer on a shared network) as if it were a local procedure call, without the programmer explicitly coding the details for remote communication. XML-RPC, JSON-RPC are simpler RPC protocols that use XML and JSON for message formatting, respectively.
But these protocols can be inefficient for high-performance systems due to their text-based nature and lack of support for streaming and multiplexing, which `gRPC` addresses with its use of Protocol Buffers and HTTP/2.

**Problem Solved:** `gRPC` addresses REST's inefficiency by using binary Protocol Buffers and HTTP/2 multiplexing, enabling ultra-fast, low-latency communication for distributed systems.
**Use Cases:** Microservices communication, real-time applications, IoT systems, internal service-to-service APIs, high-performance systems requiring low latency.

#### 4. GraphQL

**Problem Solved:** Eliminates over-fetching and under-fetching issues in REST by allowing clients to request exactly the data they need in a single query with flexible schema.
**Use Cases:** Complex data requirements, mobile applications with bandwidth constraints, real-time dashboards, aggregating data from multiple sources, rapidly evolving APIs.

#### 5. WebRTC (Web Real-Time Communication)

**Problem Solved:** Enables direct peer-to-peer communication with low latency without central servers, solving the need for real-time audio/video without plugins or dedicated infrastructure.
**Use Cases:** Video conferencing (Zoom, Hangouts), peer-to-peer file sharing, real-time gaming, live streaming, screen sharing, VoIP applications.

#### 6. Webhooks

**Problem Solved:** Eliminates polling overhead by allowing servers to push data directly to clients when events occur, enabling real-time event-driven architecture with minimal resource usage.
**Use Cases:** CI/CD pipelines, payment notifications (Stripe), real-time alerts, GitHub integrations, third-party service notifications, event-driven architectures.

- NOTE : also look at Server Sent Events (SSE)

#### 7. WebSockets

**Problem Solved:** Provides persistent, full-duplex bidirectional communication over a single connection, replacing HTTP request-response cycles for scenarios needing continuous two-way data flow.
**Use Cases:** Real-time chat applications, live notifications, collaborative tools (Google Docs), stock market tickers, multiplayer games, live dashboards, interactive applications.

---

**Reference Video:** https://youtu.be/pBASqUbZgkY?si=iFB2NM_-600RLDlJ

### Message Queues

Refs:

- https://youtu.be/1ISRd0bS714?si=gxjVYmVBVSatpA5H
- https://youtu.be/DU8o-OTeoCc?si=WM_9TbqJihnSX4dk
- https://youtu.be/1HOVtQ-_fcE?si=Bp4eQ4ZmSgsXUmiy

### Pub/Sub

### Service Mesh
