# Chapter 0: Foundations of System Design

## Why This Chapter Exists
Most system design notes jump directly into topics like load balancers, caches, CDNs, sharding, replication, and queues. That works only if the reader already understands the networking and distributed systems basics underneath them.

This chapter fills that gap. It explains the small building blocks first, then connects them to larger system design decisions.

---

## 1. What Is a Computer Network?
A **computer network** is a group of devices connected so they can exchange data.

Examples:
- a phone talking to a web server
- a browser calling an API
- one microservice talking to another
- a database replicating data to another database

### Core idea
System design is mostly about answering this question:

**How do many computers communicate, store data, and keep working reliably at scale?**

---

## 2. Internet, Web, and Server Basics

### Internet
The **internet** is a global network of networks.

### Web
The **web** is one application built on top of the internet. It uses technologies like:
- URLs
- HTTP/HTTPS
- browsers
- web servers

### Server
A **server** is a machine or process that listens for requests and sends responses.

Examples:
- web server
- application server
- database server
- cache server

### Client
A **client** is the requester.

Examples:
- browser
- mobile app
- backend service calling another backend service

---

## 3. How a Request Reaches a Server
When a user opens `https://api.example.com/users/123`, the simplified flow is:

1. The client parses the URL.
2. DNS converts `api.example.com` into an IP address.
3. The client opens a connection to that IP address on a port.
4. If HTTPS is used, TLS handshake happens.
5. The client sends an HTTP request.
6. The server processes it.
7. The server sends back an HTTP response.

This short flow already includes many core topics:
- URL
- DNS
- IP
- port
- TCP
- TLS
- HTTP
- load balancer
- reverse proxy
- application server
- database
- cache

---

## 4. URL, URI, Domain, and Endpoint

### URL
A **URL** is the address of a resource.

Example:
`https://api.example.com:443/users/123?expand=posts`

### Parts of a URL
- `https` -> protocol / scheme
- `api.example.com` -> host / domain
- `443` -> port
- `/users/123` -> path
- `expand=posts` -> query parameter

### URI
A **URI** is a broader identifier. A URL is a kind of URI.

### Endpoint
An **endpoint** is a specific API path that clients call.

Example:
- `GET /users/123`
- `POST /orders`

---

## 5. IP Address
An **IP address** identifies a device or service on a network.

Think of it as a machine's network address.

### IPv4
Example:
`192.168.1.10`

IPv4 is 32-bit, so the number of possible addresses is limited.

### IPv6
Example:
`2001:0db8:85a3:0000:0000:8a2e:0370:7334`

IPv6 is 128-bit and exists because IPv4 addresses are not enough for the whole world.

### Public vs Private IP
- **Public IP**: reachable on the internet
- **Private IP**: used inside local/private networks

Common private IPv4 ranges:
- `10.0.0.0/8`
- `172.16.0.0/12`
- `192.168.0.0/16`

### Static vs Dynamic IP
- **Static IP**: fixed
- **Dynamic IP**: may change over time

### Loopback
`127.0.0.1` or `localhost` means "this same machine."

---

## 6. Port
A **port** identifies a specific process or service on a machine.

One machine can run many services on different ports.

Examples:
- `80` -> HTTP
- `443` -> HTTPS
- `22` -> SSH
- `3306` -> MySQL
- `5432` -> PostgreSQL
- `6379` -> Redis

### IP vs Port
- IP tells you **which machine**
- port tells you **which service on that machine**

---

## 7. DNS
**DNS** stands for **Domain Name System**.

DNS maps human-readable names like `google.com` to IP addresses.

Without DNS, users would have to remember IP addresses.

### Why DNS matters in system design
- service discovery
- failover
- geo-routing
- CDN routing
- latency reduction
- traffic steering

### Basic DNS resolution flow
1. Browser or OS checks local cache.
2. Query goes to a recursive resolver.
3. Resolver may ask root server.
4. Root points to TLD server.
5. TLD points to authoritative name server.
6. Authoritative server returns the record.
7. Resolver caches and returns the answer.

### Common DNS records
- `A` -> domain to IPv4
- `AAAA` -> domain to IPv6
- `CNAME` -> alias to another domain
- `MX` -> mail server
- `TXT` -> verification / policy text
- `NS` -> name server delegation
- `PTR` -> reverse lookup

### TTL
**TTL** means **Time To Live**.
It tells resolvers how long they may cache a DNS response.

Trade-off:
- low TTL -> faster updates, more DNS lookups
- high TTL -> better cache hit rate, slower change propagation

### GeoDNS
GeoDNS routes users to different IPs based on geography.

Used in:
- multi-region deployments
- CDNs
- disaster recovery

### DNS limitations
DNS is good for routing traffic broadly, but it is not enough alone for advanced health-based routing. That is why large systems combine DNS with:
- load balancers
- health checks
- service meshes
- anycast

---

## 8. Packet
Data moving across a network is broken into smaller units called **packets**.

Each packet contains:
- payload data
- source address
- destination address
- protocol metadata

Packets may:
- take different routes
- arrive out of order
- be delayed
- be dropped

Protocols like TCP handle these problems for applications.

---

## 9. TCP and UDP

### TCP
**Transmission Control Protocol** is connection-oriented and reliable.

It provides:
- ordered delivery
- retransmission
- flow control
- congestion control

Use TCP when correctness matters more than a little extra latency.

Examples:
- HTTP/1.1
- HTTP/2
- HTTPS
- database connections

### UDP
**User Datagram Protocol** is connectionless and lightweight.

It provides:
- low overhead
- no built-in delivery guarantee
- no ordering guarantee

Use UDP when speed matters and the application can tolerate loss or handle it itself.

Examples:
- DNS queries
- live audio/video
- online gaming
- telemetry

### TCP vs UDP
- TCP: reliable, ordered, heavier
- UDP: faster, simpler, less reliable

---

## 10. Connection, Handshake, and Latency

### Connection
A **connection** is a communication session between two endpoints.

### TCP handshake
TCP usually starts with a **3-way handshake**:
- SYN
- SYN-ACK
- ACK

This establishes that both sides are ready.

### TLS handshake
If HTTPS is used, a TLS handshake happens after TCP to:
- authenticate the server
- negotiate encryption keys
- secure the session

### Latency
**Latency** is the time taken for data to travel and a response to come back.

Types:
- network latency
- disk latency
- processing latency
- queueing latency

Low latency is critical for:
- chat
- gaming
- autocomplete
- payments

---

## 11. Bandwidth, Throughput, and IOPS

### Bandwidth
Maximum data transfer capacity of a link.

### Throughput
Actual amount of useful work or data transferred per unit time.

### IOPS
**Input/Output Operations Per Second**, mainly used for storage systems.

### Difference
A system may have high bandwidth but low throughput if:
- packets are lost
- server is overloaded
- application is inefficient

---

## 12. HTTP and HTTPS

### HTTP
**HyperText Transfer Protocol** is an application-layer protocol used by web clients and servers.

It is request-response based.

### Request
Contains:
- method
- path
- headers
- optional body

### Response
Contains:
- status code
- headers
- optional body

### Common HTTP methods
- `GET` -> read data
- `POST` -> create / submit
- `PUT` -> replace
- `PATCH` -> partial update
- `DELETE` -> remove

### Common status codes
- `200 OK`
- `201 Created`
- `204 No Content`
- `301 Moved Permanently`
- `302 Found`
- `400 Bad Request`
- `401 Unauthorized`
- `403 Forbidden`
- `404 Not Found`
- `409 Conflict`
- `429 Too Many Requests`
- `500 Internal Server Error`
- `502 Bad Gateway`
- `503 Service Unavailable`

### Headers
Metadata attached to requests and responses.

Examples:
- `Authorization`
- `Content-Type`
- `Cache-Control`
- `Cookie`
- `User-Agent`

### Statelessness
HTTP is considered **stateless** because each request is independent by default.

If session state is needed, systems use:
- cookies
- tokens
- shared session stores
- databases

### HTTPS
HTTPS is HTTP over TLS.

It provides:
- encryption
- integrity
- server authentication

Without HTTPS:
- traffic can be read
- traffic can be altered
- users can be impersonated through man-in-the-middle attacks

---

## 13. TLS / SSL
People often say **SSL**, but modern systems actually use **TLS**.

### TLS provides
- confidentiality
- integrity
- authentication

### Certificates
A **TLS certificate** proves that a server controls a domain.

Certificates are issued by **Certificate Authorities (CAs)**.

### Why TLS matters in system design
- protects user data
- secures login/session tokens
- required for browsers and APIs
- used internally between services in high-security systems

### TLS termination
Often TLS is terminated at:
- load balancer
- reverse proxy
- ingress controller

Then internal traffic may be:
- plaintext inside trusted private network
- re-encrypted using mTLS

### mTLS
**Mutual TLS** means both client and server authenticate each other.
Common in service-to-service communication.

---

## 14. Session, Cookie, and Token

### Cookie
Small data stored by the browser and sent automatically with requests to a domain.

Used for:
- session IDs
- preferences
- tracking

### Session
A **session** is server-side state associated with a user.

Example:
- user logs in
- server creates session ID
- browser stores it in cookie
- later requests carry session ID

### Token
A token is usually a self-contained credential or opaque access string.

Examples:
- JWT
- OAuth access token
- refresh token

### Stateful vs Stateless auth
- **Stateful auth**: server stores session state
- **Stateless auth**: token carries claims, server verifies signature

Trade-off:
- stateful auth is easier to revoke centrally
- stateless auth scales better in some cases

---

## 15. API, REST, RPC, and gRPC

### API
An **API** is a contract for how one software component talks to another.

### REST
REST-style APIs usually:
- use HTTP
- organize resources by URLs
- use standard verbs like GET/POST

### RPC
**Remote Procedure Call** models communication as calling a remote function.

### gRPC
A high-performance RPC framework commonly using:
- Protocol Buffers
- HTTP/2
- binary payloads

### REST vs gRPC
- REST: simpler for public APIs, easier for browsers
- gRPC: efficient for service-to-service communication

---

## 16. Proxy, Reverse Proxy, and Load Balancer

### Proxy
A **proxy** sits between a client and another server.

### Forward proxy
Acts on behalf of the client.

Examples:
- corporate proxy
- privacy proxy

### Reverse proxy
Acts on behalf of servers.

Common jobs:
- TLS termination
- routing
- caching
- compression
- authentication checks

Examples:
- Nginx
- HAProxy
- Envoy

### Load balancer
A **load balancer** distributes traffic across multiple backend instances.

Benefits:
- scale out
- avoid overload on one server
- improve availability
- support rolling deployments

### Load balancing algorithms
- round robin
- weighted round robin
- least connections
- hash-based routing
- consistent hashing

### L4 vs L7 load balancer
- **Layer 4**: routes using IP and port
- **Layer 7**: routes using HTTP details like host, path, headers

---

## 17. Firewall, NAT, and Subnet

### Firewall
A firewall allows or blocks traffic according to rules.

### NAT
**Network Address Translation** lets many private devices share fewer public IPs.

### Subnet
A subnet is a smaller logical partition inside a network.

Used for:
- organization
- isolation
- security
- routing control

### Why these matter in system design
Cloud architectures depend heavily on:
- public subnets
- private subnets
- security groups
- ingress/egress rules

---

## 18. CDN
A **Content Delivery Network** is a globally distributed cache for content.

Usually used for:
- images
- CSS
- JavaScript
- video
- downloadable files
- API edge acceleration

### Benefits
- lower latency
- reduced origin load
- higher availability
- better global performance

### Important concepts
- edge location
- origin server
- cache hit
- cache miss
- cache invalidation
- TTL

### Common mistake
People think CDN is only for static websites. In reality, CDNs are heavily used in:
- streaming
- APIs
- security filtering
- DDoS mitigation

---

## 19. Cache
A **cache** stores data in a faster layer so repeated access is cheaper.

### Why cache is useful
Main memory is much faster than disk or remote database calls.

### Where caches exist
- browser cache
- CDN cache
- reverse proxy cache
- application cache
- database cache
- CPU cache

### Cache patterns
- **Cache-aside**: app checks cache first, then DB
- **Read-through**: cache fetches data on miss
- **Write-through**: writes go to cache and backing store together
- **Write-back**: cache accepts write first, persists later

### Cache problems
- stale data
- cache stampede
- hot keys
- cache penetration
- cache eviction mistakes

### Eviction policies
- LRU
- LFU
- FIFO
- TTL-based eviction

---

## 20. Database Basics
A **database** stores and retrieves data reliably.

### SQL databases
Relational systems like:
- MySQL
- PostgreSQL
- Oracle

Strong for:
- structured schema
- joins
- transactions
- consistency

### NoSQL databases
Broad category including:
- key-value stores
- document stores
- column-family stores
- graph databases

Good when you need:
- flexible schema
- huge scale
- specific access patterns
- low-latency simple lookups

### Schema
A **schema** defines how data is organized.

### Index
An **index** speeds up lookups by avoiding full scans.

Trade-off:
- faster reads
- extra storage
- slower writes

---

## 21. Transaction, ACID, and Isolation

### Transaction
A transaction is a group of operations treated as one unit.

### ACID
- **Atomicity**: all or nothing
- **Consistency**: valid state transitions
- **Isolation**: concurrent transactions do not corrupt each other
- **Durability**: committed data survives crashes

### Isolation levels
Common levels:
- read uncommitted
- read committed
- repeatable read
- serializable

Higher isolation usually gives stronger correctness but lower concurrency.

---

## 22. Replication
**Replication** means keeping copies of data on multiple nodes.

### Why replicate?
- high availability
- read scaling
- fault tolerance
- disaster recovery

### Primary-replica
- primary handles writes
- replicas serve reads and copy changes from primary

### Synchronous vs asynchronous replication
- **Synchronous**: stronger consistency, slower writes
- **Asynchronous**: faster writes, possible replication lag

### Replication lag
Delay before replicas reflect the latest write.

This creates problems like:
- stale reads
- read-after-write inconsistency

---

## 23. Partitioning and Sharding

### Partitioning
Splitting data into pieces.

### Sharding
Horizontal partitioning across multiple machines.

Example:
- users `1-1,000,000` on shard A
- users `1,000,001-2,000,000` on shard B

### Why shard?
- store more data
- spread traffic
- avoid single-machine limits

### Sharding key
The field used to decide where data goes.

Good sharding key properties:
- even distribution
- high cardinality
- aligned with query pattern

### Sharding risks
- hot shards
- resharding complexity
- cross-shard joins
- fan-out queries

---

## 24. Consistency
**Consistency** in distributed systems can mean different things depending on context.

### Database consistency in ACID
The database remains valid according to rules and constraints.

### Replica consistency
All copies eventually converge or stay strongly synchronized.

### Strong consistency
After a write completes, all later reads see that write.

### Eventual consistency
Replicas may temporarily disagree, but they converge later.

### Read-your-writes consistency
A user should see their own latest writes.

### Monotonic reads
Reads should not appear to go backward in time.

These guarantees matter in:
- social feeds
- chat
- payments
- collaborative editing

---

## 25. Availability, Reliability, and Fault Tolerance

### Availability
System is up and can respond.

### Reliability
System behaves correctly over time.

### Fault tolerance
System continues working even when some parts fail.

### Redundancy
Extra components added so one failure does not kill the system.

Examples:
- multiple app servers
- multiple replicas
- multi-AZ deployments
- backups

---

## 26. CAP Theorem
In a distributed system, during a network partition, you cannot fully maximize all three:
- **Consistency**
- **Availability**
- **Partition tolerance**

### Important nuance
Modern explanations often simplify CAP too much.

Real point:
If the network splits, you must choose whether to:
- reject some operations to preserve consistency
- accept some operations to preserve availability

You do not get to ignore partition tolerance in real distributed systems.

---

## 27. Quorum
A **quorum** means enough replicas agree for an operation to count.

Common notation:
- `N` = total replicas
- `W` = write quorum
- `R` = read quorum

If `R + W > N`, you can often get stronger read/write overlap guarantees.

Used in:
- Dynamo-style stores
- consensus-like systems
- distributed metadata stores

---

## 28. Leader Election and Consensus

### Leader election
Nodes choose one leader to coordinate certain work.

### Consensus
Nodes agree on shared state despite failures.

Common algorithms:
- Raft
- Paxos

Used in:
- metadata services
- lock managers
- configuration stores
- distributed databases

Examples:
- ZooKeeper
- etcd
- Consul

---

## 29. Message Queue, Stream, and Event

### Message queue
A durable buffer between producers and consumers.

Benefits:
- decoupling
- async processing
- retry support
- traffic smoothing

Examples:
- RabbitMQ
- SQS

### Stream
An ordered append-only log of events over time.

Examples:
- Kafka
- Pulsar

### Event
Something that happened.

Examples:
- order placed
- payment failed
- user logged in

### Queue vs stream
- Queue: work distribution
- Stream: ordered event history and replay

---

## 30. Synchronous vs Asynchronous Processing

### Synchronous
Caller waits immediately for the result.

Good for:
- login
- payment authorization
- search query response

### Asynchronous
Caller does not wait for the full work to finish.

Good for:
- email sending
- video transcoding
- report generation
- notifications

Trade-off:
- sync is simpler for users
- async improves resilience and throughput for heavy work

---

## 31. Throughput, Scalability, and Bottlenecks

### Throughput
How much work a system completes per unit time.

### Scalability
Ability to keep handling more load by adding resources or redesigning parts of the system.

### Bottleneck
The part limiting the whole system.

Examples:
- CPU saturation
- database lock contention
- disk I/O
- network bandwidth
- hot partition

### Rule
System design is mostly bottleneck management.

---

## 32. Vertical and Horizontal Scaling

### Vertical scaling
Add more CPU, RAM, or disk to the same machine.

Pros:
- simple

Cons:
- hardware limit
- expensive
- single-machine dependency

### Horizontal scaling
Add more machines.

Pros:
- better scale ceiling
- better redundancy

Cons:
- more distributed-system complexity

---

## 33. Stateless vs Stateful Services

### Stateless service
Each request can be served by any instance because no important per-user state is kept only in local memory.

Examples:
- many API servers

### Stateful service
Server keeps important session or connection state locally.

Examples:
- websocket server with live connection state
- database leader

### Why this matters
Stateless services are easier to:
- scale
- replace
- auto-heal
- load balance

---

## 34. WebSocket, Polling, and Long Polling

### Polling
Client repeatedly asks server for updates.

### Long polling
Server keeps request open until data is available or timeout occurs.

### WebSocket
Persistent full-duplex connection between client and server.

Good for:
- chat
- live dashboards
- multiplayer collaboration

Trade-off:
- more efficient for real-time
- more stateful and operationally complex

---

## 35. Idempotency
An operation is **idempotent** if repeating it produces the same final result.

Very important in distributed systems because retries happen often.

Examples:
- setting profile name to "Aman" repeatedly
- charging a card is **not** naturally idempotent unless protected by idempotency key

Used in:
- payment systems
- APIs with retries
- message consumers

---

## 36. Retry, Timeout, Circuit Breaker, and Backoff

### Timeout
Maximum time a caller waits.

### Retry
Try again after failure.

### Backoff
Increase wait time between retries.

### Circuit breaker
Stop sending requests temporarily to an unhealthy dependency.

These patterns prevent:
- infinite waiting
- retry storms
- cascading failures

---

## 37. Rate Limiting
**Rate limiting** controls how many requests are allowed in a time period.

Used for:
- abuse prevention
- fairness
- cost protection
- overload prevention

Common algorithms:
- token bucket
- leaky bucket
- fixed window
- sliding window log
- sliding window counter

This repo has a separate full chapter on this topic.

---

## 38. Monitoring, Logging, Metrics, and Tracing

### Logging
Detailed event records.

### Metrics
Numeric measurements over time.

Examples:
- request rate
- error rate
- p99 latency
- CPU usage

### Tracing
Tracks a request across many services.

Useful in microservices to find:
- latency hotspots
- failed dependencies
- request path

### Golden signals
Often monitored:
- latency
- traffic
- errors
- saturation

---

## 39. SLA, SLO, and SLI

### SLA
**Service Level Agreement** is an external commitment.

### SLO
**Service Level Objective** is an internal target.

### SLI
**Service Level Indicator** is the measured metric.

Example:
- SLI: request success rate
- SLO: 99.9% monthly success
- SLA: contractual promise to customer

---

## 40. Data Center, Region, AZ, and Multi-Region

### Data center
Physical facility containing servers and network equipment.

### Region
A geographic area with one or more data centers.

### Availability Zone (AZ)
An isolated location within a region.

### Multi-AZ
Protects against one data center failure.

### Multi-region
Protects against region-level outages and reduces global latency.

Trade-offs:
- more resilience
- more operational complexity
- harder data consistency

---

## 41. Common Terms You Will See in System Design

### Latency
Time to complete one request.

### Throughput
Requests or operations per second.

### p50 / p95 / p99
Percentile latency measures.

### Hot key
One key gets far more traffic than others.

### Hot partition
One shard gets too much traffic or data.

### Fan-out
One request causes work to spread to many downstream systems.

### Fan-in
Many inputs combine into one result.

### Backpressure
Technique to slow producers when consumers cannot keep up.

### Thundering herd
Many clients retry or refresh at the same time.

### Stampede
Many requests hit backend at once after cache expiry.

### Single Point of Failure (SPOF)
One component whose failure brings down the whole system.

---

## 42. How These Foundations Map to the Rest of This Repo

### Scaling
Requires understanding:
- DNS
- load balancer
- cache
- CDN
- replication
- stateless services
- sharding

### Rate Limiter
Requires understanding:
- time windows
- counters
- atomic updates
- distributed coordination

### Consistent Hashing
Requires understanding:
- partitioning
- load distribution
- scaling

### Key-Value Store
Requires understanding:
- replication
- quorum
- consistency
- partition tolerance

### URL Shortener
Requires understanding:
- HTTP redirects
- database keys
- cache

### Chat System
Requires understanding:
- WebSocket
- presence
- message ordering
- fan-out
- low latency

### YouTube and Google Drive
Require understanding:
- CDN
- blob storage
- queues
- async processing
- metadata databases
- multi-region concerns

---

## 43. Beginner-to-Advanced Learning Path

### Beginner
Start with:
- client and server
- URL
- DNS
- IP and port
- HTTP/HTTPS
- database basics
- cache basics
- load balancer basics

### Intermediate
Then learn:
- TCP vs UDP
- TLS
- reverse proxy
- replication
- sharding
- queues
- stateless vs stateful services
- CDN

### Advanced
Then move to:
- consistency models
- quorum
- CAP theorem
- leader election
- consensus
- multi-region design
- fault tolerance patterns
- backpressure
- observability

---

## 44. Fast Mental Model for Interviews
When you hear a system design problem, think in this order:

1. Who are the clients?
2. What requests do they send?
3. How do requests reach the system?
4. Which services process them?
5. Where is data stored?
6. What should be cached?
7. What needs async processing?
8. Where can failures happen?
9. How does the system scale?
10. Which trade-offs are acceptable?

---

## 45. Final Summary
If you understand the topics in this chapter, the rest of system design becomes much easier.

At minimum, you should be comfortable explaining:
- what DNS does
- what an IP address and port are
- how HTTP request-response works
- why HTTPS uses TLS
- what a load balancer does
- when to use cache
- why replication exists
- why sharding exists
- what consistency and availability mean
- why queues and async processing are useful

That is the base layer for nearly every modern backend system.

---

## 46. How to Study This Chapter
### Phase 1: Vocabulary
Make sure you can explain these clearly in one sentence each:
- client
- server
- DNS
- IP address
- port
- TCP
- UDP
- HTTP
- HTTPS
- TLS
- cache
- replication
- sharding
- queue

### Phase 2: Request Journey
Practice explaining this full path:
1. user enters URL
2. DNS resolves domain
3. client opens connection to IP and port
4. TLS handshake secures the connection
5. HTTP request reaches reverse proxy or load balancer
6. app server processes request
7. cache and database are consulted
8. response is returned

### Phase 3: Distributed Systems Thinking
Once the basics are clear, practice these trade-offs:
- consistency vs availability
- latency vs durability
- read optimization vs write complexity
- vertical scaling vs horizontal scaling

---

## 47. Practice Questions
1. What is the difference between a domain, an IP address, and a port?
2. Why does DNS use caching, and what role does TTL play?
3. Why is HTTPS preferred over HTTP?
4. When would you choose TCP over UDP?
5. What problems does a reverse proxy solve?
6. Why is cache invalidation difficult?
7. What is the difference between replication and sharding?
8. What does eventual consistency mean in user-visible terms?
9. Why do message queues improve reliability?
10. Why are stateless services easier to scale?

---

## 48. Example Walkthrough
### Example: Opening `https://example.com/profile`
1. The browser parses the URL and identifies the scheme as HTTPS.
2. DNS resolves `example.com` to an IP address.
3. The browser opens a TCP connection to the resolved IP on port `443`.
4. TLS handshake establishes encryption and verifies the server certificate.
5. The browser sends an HTTP request such as `GET /profile`.
6. A load balancer or reverse proxy forwards the request to an application server.
7. The application server may check cache first and then query a database if needed.
8. The response returns through the same chain back to the browser.

## 49. Exercises
1. Explain the difference between TCP and UDP with one real-world example for each.
2. Explain why DNS alone is not enough for advanced failover.
3. Describe what happens when cached data becomes stale.
4. Explain the difference between replication and sharding in one paragraph.

---

## 50. One-Minute Revision
- DNS maps names to IPs.
- IP identifies the machine; port identifies the service.
- TCP is reliable and ordered; UDP is lightweight and best-effort.
- HTTPS = HTTP + TLS.
- Cache speeds up reads but creates invalidation problems.
- Replication copies data; sharding splits data.
- Queues decouple systems and smooth bursts.

## 51. Exercise Answers
1. TCP gives reliable ordered delivery and is used by HTTP. UDP is faster and lighter and is often used for DNS or real-time media.
2. DNS helps route traffic broadly, but advanced failover usually also needs health checks, load balancers, and traffic steering logic.
3. When cache becomes stale, users may read old data until the cache entry expires, is invalidated, or is refreshed from the source of truth.
4. Replication keeps multiple copies of the same data for availability and reads. Sharding splits different parts of the dataset across multiple machines for scale.
