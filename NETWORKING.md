Networking System Design Notes

- [Latency and Throughput - Fundamental Performance Metrics](#latency-and-throughput)
- [DNS - Domain Name System (resolvers, nameservers, records)](#dns---domain-name-system)
- [Load Balancers - Hardware, software, Layer 4, Layer 7](#load-balancers)
- [CDNs - Content Delivery Networks (caching, edge servers)](#content-delivery-networks)
- [Proxies - Forward, reverse, transparent, anonymous](#proxies)
- [Gateways - Connect different networks](#gateways)

- VPNs - Virtual Private Networks (tunneling protocols) : https://youtu.be/R-JUOpCgTZc?si=2-4F9EnKRe71Gwsm
- Firewalls - Packet filtering, stateful inspection
- NAT - Network Address Translation
- Routers - Direct traffic between networks

### Latency and Throughput

- What is `Latency`?
  - `Latency` is the **time it takes for data to travel from source to destination**. It's measured in milliseconds (ms), microseconds (µs), or nanoseconds (ns).
  - Think of it as the **delay** between when you send a request and when you receive the first part of the response.
  - **Simple Analogy**: If you call a friend on phone, latency is the time from when you speak until they hear you.

- Real-World Example of Latency:

  ```
  User clicks on a link on Google.com
  ├─ Request sent to server: 5ms (network travel time)
  ├─ Server processes request: 10ms
  ├─ Response sent back: 5ms (network travel time)
  └─ Total Latency: ~20ms
  ```

  This 20ms is felt as "responsiveness" - good UX if near 0, bad UX if >500ms.

- Types of Latency:
  - `Network Latency`: Time for data to travel through network cables
    - India to USA: ~150-200ms
    - India (Delhi) to India (Mumbai): ~10-20ms
    - Same data center: <1ms
  - `Application Latency`: Time for server to process request
    - Simple database query: 1-5ms
    - Complex calculation: 50-100ms
    - ML model inference: 500ms-2s
  - `End-to-End Latency`: Network + Application latency combined

- What is `Throughput`?
  - `Throughput` is the **amount of data that can be transferred in a given time period**. It's measured in bits per second (bps), kilobits per second (kbps), megabits per second (Mbps), etc.
  - Think of it as the **bandwidth** or **capacity** of a connection.
  - **Simple Analogy**: If latency is how fast you dial a phone, throughput is how many phone calls you can make simultaneously.

- Real-World Example of `Throughput`:

  ```
  Your home internet: 100 Mbps (Megabits per second)
  ├─ Download speed: 100 Mbps = ~12.5 MB/s
  ├─ Can download a 1 GB file in: ~80 seconds
  └─ Can stream 4K video (25 Mbps required): YES (100 / 25 = 4 simultaneous streams)
  ```

- Understanding the Difference with Examples:

  **Example 1: Downloading a File**
  - File size: 100 MB
  - Latency: 50ms (delay to establish connection)
  - Throughput: 10 Mbps (download speed)

  ```
  Time = Latency + (File Size × 8 bits) / Throughput
  Time = 50ms + (100 MB × 8) / 10 Mbps
  Time = 50ms + 80 seconds = ~80 seconds
  (Latency is negligible compared to transfer time)
  ```

  **Example 2: Web Page Load**
  - Page consists of 100 small files (HTML, CSS, JS, images)
  - Latency: 100ms per request (includes DNS, connection setup)
  - Throughput: 100 Mbps

  ```
  Time = (100 requests × 100ms latency) + Transfer time
  Time = 10 seconds + small transfer = ~10 seconds
  (Latency dominates! Each file needs a separate round-trip)

  → This is why HTTP/2 and HTTP/3 use multiplexing (send multiple files parallelly)
  ```

  **Example 3: Gaming Server**
  - Latency requirement: <50ms (for smooth gameplay)
  - Throughput requirement: 1 Mbps (position updates, actions)

  ```
  If latency > 100ms → Players notice lag, game feels unresponsive
  If throughput < 1 Mbps → Game packets get queued, additional lag

  → BOTH latency AND throughput matter for user experience
  ```

- Real-World Latency & Throughput Numbers:

  | Scenario                | Latency  | Throughput | Impact                                                       |
  | ----------------------- | -------- | ---------- | ------------------------------------------------------------ |
  | Video Call (Zoom/Teams) | <150ms   | 2-5 Mbps   | Acceptable, any higher latency sounds awkward                |
  | Online Gaming (FPS)     | <50ms    | 1-10 Mbps  | <50ms = smooth, >100ms = unplayable                          |
  | Web Browsing            | <200ms   | 5-50 Mbps  | Pages load quickly                                           |
  | YouTube 4K Streaming    | <1000ms  | 25 Mbps    | Buffering OK if latency high, but need consistent throughput |
  | Stock Trading           | <10ms    | 1-10 Mbps  | Microseconds matter for HFT!                                 |
  | File Download           | <100ms   | 100+ Mbps  | Throughput dominates transfer time                           |
  | Cloud Database Query    | <10-50ms | 1-5 Mbps   | Both matter for responsiveness                               |

- Why Both Matter in System Design:

  **Optimization Strategies:**
  - **Low Latency**: Use CDNs, edge servers, local caching
    - Example: Netflix uses CDNs so videos load from ISP server (1-5ms) not Netflix server (100ms)
  - **High Throughput**: Better network infrastructure, load balancing, data compression
    - Example: YouTube compresses videos to reduce throughput needs
  - **Trade-offs**: Sometimes you sacrifice throughput for latency
    - Example: Trading systems: Use expensive low-latency networks, accept lower throughput
  - **Trade-offs**: Sometimes you sacrifice latency for throughput
    - Example: Batch processing systems: Accept 1-hour latency to process billions of records

- Practical Latency Targets in System Design:

  ```
  1ms    ← Ultra-low latency trading systems
  10ms   ← Real-time competitive gaming
  50ms   ← Video calls acceptable
  100ms  ← Web applications feel quick
  200ms  ← Web applications acceptable
  500ms  ← Users perceive slowness
  1000ms ← Users think system is broken
  ```

- How to Measure Latency:
  - Terminal command: `ping google.com`
  - Network tools: Wireshark, tcpdump, netcat
  - Application monitoring: New Relic, DataDog, Prometheus
  - Browser DevTools: F12 → Network tab (shows request/response time)

- How to Measure Throughput:
  - Terminal command: `iperf3` (network throughput benchmark)
  - Speed test websites: speedtest.net
  - Browser download test: Download a large file in browser, check speed
  - Command: `time curl -O https://example.com/largefile.iso`

- Routers - Direct traffic between networks

### DNS - Domain Name System

- What is DNS and how does it work?
  - [DNS Servers](https://www.youtube.com/watch?v=JkEYOt08-rU)
  - Computers identitfy each others using IP addresses, but humans prefer to use domain names. DNS translates domain names into IP addresses.

- What are key components of DNS?
  - `Resolvers`: Client-side software that initiates DNS queries. - `Recursive resolvers`: Perform the full resolution process on behalf of the client. These are typically provided by ISPs( Jio, Airtel, Act) or public DNS services (e.g., Google DNS, Cloudflare).
  - `Nameservers`: Servers that store DNS records and respond to queries.
    - `Root nameservers`: Top-level servers that direct queries to TLD(Top-level-domain) nameservers, .
    - `TLD nameservers`: Handle queries for specific top-level domains (e.g., .com, .org, .net, .in, .edu).
    - `Authoritative nameservers`: Store DNS records for specific domains and provide answers to queries about those domains.
  - `DNS Records`: Types include A (IPv4), AAAA (IPv6), CNAME (alias), MX (mail exchange), NS (name server).

  - How does TLD know which authoritative nameservers to direct to?
    - This all starts when we register our domain at GoDaddy or Namecheap. When we register a domain, we provide the nameservers that will be authoritative for that domain. The registrar then updates the TLD nameservers with NS records that point to the authoritative nameservers for our domain. So when a query reaches a TLD nameserver, it looks up the NS record for the requested domain and directs the resolver to the appropriate authoritative nameservers.

- How does DNS resolution work?
  1. User enters domain name in browser.
     - This domain name is passed to OS
     - Your OS is mapped to DNS resolver/servers (configured in network settings).
  2. Resolver checks local cache for IP address.
  3. If not found, resolver queries root nameservers.
  4. Root nameservers direct to TLD nameservers (e.g., .com). [Top Level Domain servers]
  5. TLD nameservers direct to authoritative nameservers for the domain.
     - Authoritative nameservers have the DNS records for the domain and can provide the IP address.
  6. Authoritative nameservers return the IP address to the resolver.
  7. Resolver returns IP address to the user's browser, which connects to the server.

- Do we need DNS server?
  If you know the IP address of the server you want to connect to, you can connect directly without using DNS. How many IP's will you remember? 😄 😄
  However, DNS is essential for human-friendly domain names and for managing large-scale networks efficiently.

### Load Balancers

- What is a load balancer and why is it used?
  - [Load Balancer](https://www.youtube.com/watch?v=1fN2UDbtGDQ)
  - `Real-World Example`:
    Imagine a video streaming service with 1000 concurrent users. Instead of all users hitting one server (which would crash), a load balancer distributes them across 10 servers, each handling ~100 users.

- Why Should we use a Load Balancer?
  - `Scalability`: Distributes traffic across multiple servers, allowing the system to handle more users.
  - `High Availability`: If one server fails, the load balancer can redirect traffic to healthy servers, ensuring uptime.
  - `Performance`: By balancing the load, it prevents any single server from becoming a bottleneck, improving response times.
  - `Fault Tolerance`: Continue serving requests even with server failures

- How does a load balancer work?
  - A load balancer sits between clients and servers, receiving incoming requests and distributing them based on predefined algorithms. It monitors the health of servers and can automatically reroute traffic if a server becomes unavailable.
  - The algorithms can be `static algorithm` or `dynamic algorithm`.
  - `Static algorithms` do not consider the current state of the servers, they just follow the rules irrespective of server state. Eg: Round Robin, IP Hash, Weighted Round Robin.
    - `Round Robin`: Distributes requests sequentially across servers.
    - `IP Hash`: Routes requests based on the client's IP address, ensuring session persistence.
    - `Weighted Round Robin`: Assigns weights to servers based on their capacity, directing more traffic to stronger servers.
  - `Dynamic algorithms` take into account factors like server load and response times, takes server state into account. Eg: Least Connections, Least Response Time.
    - `Least Connections`: Directs traffic to the server with the fewest active connections.
    - `Least Response Time`: Routes requests to the server with the fastest response time.
    - `Resource Based Algorithm`: Routes requests based on real-time resource utilization (CPU, memory(RAM)) of servers.

  - Types Of Load Balancers:
    - `Hardware Load Balancers`: Physical devices that manage traffic (e.g., F5, Citrix).
    - `Software Load Balancers`: Software-based solutions that can run on commodity hardware (e.g., HAProxy, Nginx).
    - `Cloud-Based Load Balancers`: Managed services provided by cloud providers (e.g., AWS ELB, Azure Load Balancer, Google Cloud Load Balancer).

- Layer 4 vs Layer 7 Load Balancing:
  - `Layer 4 Load Balancing`: Operates at the `transport layer (TCP/UDP)`. It makes routing decisions based on IP address, TCP port, and UDP port. It is faster but less flexible.
    - `Layer 7 Load Balancing`: Operates at the `application layer (HTTP/HTTPS)`. It can make routing decisions based on content, such as URL paths, headers, cookies, and more. It is more flexible but can introduce additional latency.

- When to use Layer 4 vs Layer 7 Load Balancing?
  - Use `Layer 4` when you need high performance and are routing based on IP/port without needing to inspect the content of the traffic.
  - Use `Layer 7` when you need advanced routing capabilities based on application-level data, such as directing traffic to different servers based on URL paths or headers.

- Real-Time Examples: Layer 4 vs Layer 7 Load Balancers

  **Example 1: E-Commerce Platform (Layer 7)**
  - Scenario: Amazon handles multiple services through a single domain (amazon.com).
    - `/products` requests → Route to Product Search Servers
    - `/checkout` requests → Route to Payment Servers (higher performance, security focus)
    - `/api/recommendations` requests → Route to ML Recommendation Servers
  - Why Layer 7?
    - URL path-based routing: Different services need different infrastructure.
    - Session awareness: User login headers ensure they reach consistent servers.
    - Cookie inspection: Marketing data routing based on user preferences.
  - Tools Used: NGINX, AWS ALB (Application Load Balancer), HAProxy with HTTP inspection.

  **Example 2: Real-Time Trading Platform (Layer 4)**
  - Scenario: Stock exchange like NSE or BSE needs ultra-fast order routing.
    - Incoming orders on port 8001 → Distribute across 10 order processing servers
    - Market data on port 8002 → Distribute across 5 data feed servers
    - Price quotes on port 8003 → Route to quote aggregation servers
  - Why Layer 4?
    - Milliseconds matter: Layer 4 adds <1ms latency vs Layer 7's 5-50ms.
    - Simple binary protocol: No need for HTTP inspection.
    - Massive throughput: Millions of orders/second without content inspection overhead.
  - Tools Used: Hardware LBs (F5, Citrix Netscaler), HAProxy TCP mode.

  **Example 3: Video Streaming Service (Hybrid Approach)**
  - Scenario: Netflix-like platform with mixed requirements.
    - Static content (videos) `/stream/*` → Layer 4 LB (for speed)
      - TCP port 1935 → RTMP streaming servers
      - All requests identical: Just balance by connection count
    - Dynamic requests `/api/user-profile`, `/recommendations` → Layer 7 LB
      - Route based on endpoint, user tier, geographic location
      - VIP users → Premium servers
  - Why Mix?
    - Video delivery: Latency-sensitive, doesn't need content inspection.
    - Backend API: Controller processing needed, optimize per endpoint.

  **Example 4: Mobile Gaming Backend (Layer 4 with Sticky Sessions)**
  - Scenario: PUBG Mobile servers handle millions of concurrent players.
    - All game clients connect to port 9999 (game traffic protocol)
    - UDP-based communication (not HTTP)
  - Layer 4 Benefits:
    - UDP support only (no HTTP layer available)
    - Connection stickiness: Route same player to same game server (maintains game state)
    - Low latency: IP-hash algorithm ensures consistent routing
  - Example routing:
    ```
    Player 1 from IP 203.0.113.5 → Game Server 3
    Player 2 from IP 203.0.113.6 → Game Server 1
    (Always same server for session persistence)
    ```

  **Example 5: Microservices Architecture with API Gateway (Layer 7)**
  - Scenario: Ride-sharing app (Uber) with 50+ microservices.
    - `/users/profile` → User Service
    - `/rides/nearby` → Ride Matching Service
    - `/payments/process` → Payment Service
    - `/support/chat` → Support Service
  - Layer 7 Intelligence:
    - Header inspection: Route premium users to faster servers
    - Request body analysis: Route surge pricing requests to optimized servers
    - Custom header rules:
      ```
      If header "X-Premium-User: true" → Premium Server Fleet
      If URL contains "/admin/" → Admin Server Fleet
      If header "X-API-Version: v2" → V2 API Servers
      ```

  **Example 6: DNS Server Cluster (Layer 4)**
  - Scenario: Google Public DNS (8.8.8.8) handles billions of queries.
    - All requests on UDP port 53
    - Queries are stateless, simple
    - Need for speed: Route by IP hash to nearest server
  - Why not Layer 7?
    - DNS protocol not HTTP-based
    - Inspection overhead would slow query resolution
    - Simple IP-based routing sufficient

  **Real-Time Performance Comparison:**
  - Layer 4 Latency: 0.1-1 ms (minimal processing)
  - Layer 7 Latency: 5-50 ms (content inspection, parsing)

  | Metric           | Layer 4                   | Layer 7                       |
  | ---------------- | ------------------------- | ----------------------------- |
  | Latency          | <1ms                      | 5-50ms                        |
  | Throughput       | Millions req/sec          | Hundreds of thousands req/sec |
  | Use Case         | Games, Trading, Streaming | APIs, Web Apps, Microservices |
  | Cost             | Lower (hardware)          | Higher (CPU intensive)        |
  | Flexibility      | Low (IP/Port only)        | High (URL, Headers, Cookies)  |
  | Protocol Support | TCP/UDP any protocol      | HTTP/HTTPS (usually)          |

### Content Delivery Networks

- What is a CDN and why is it used?
  - [CDN](https://www.youtube.com/watch?v=nhhfSBm6v4A)
  - A CDN is a network of distributed servers that cache and deliver content to users based on their geographic location. It is used to improve the performance, availability, and scalability of web applications by reducing latency and offloading traffic from origin servers.

- How does a CDN work?
  1. When a user requests content (e.g., a webpage, video, image), the request is routed to the nearest CDN `edge server` based on the user's geographic location.
  2. The edge server checks if it has the requested content in its cache.
     - If the content is cached, it is delivered directly to the user, resulting in low latency.
     - If the content is not cached, the edge server retrieves it from the origin server, caches it for future requests, and then delivers it to the user.
  3. The CDN uses various techniques to optimize content delivery, such as:
     - Caching static assets (images, CSS, JavaScript)
     - Dynamic content acceleration (e.g., using TCP optimizations)
  - What are PoP and Edge Servers?
    - `PoP (Point of Presence)`: A location where the CDN has a presence, typically consisting of multiple edge servers. PoPs are strategically placed around the world to ensure low latency for users.
    - `Edge Servers`: The servers located at the PoPs that cache and deliver content to users. They handle incoming requests and serve cached content or fetch it from the origin server as needed.

- Benefits of using a CDN:
  - `Reduced Latency`: By serving content from edge servers closer to users, CDNs significantly reduce the time it takes for content to load.
  - `Improved Performance`: CDNs can optimize content delivery using techniques like compression, minification, and protocol optimizations.
  - `Scalability`: CDNs can handle large volumes of traffic and sudden spikes without overwhelming the origin server

### Proxies

### Gateways
