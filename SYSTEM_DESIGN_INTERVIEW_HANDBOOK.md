# System Design Interview Handbook

## Purpose
This handbook is a fast revision guide for system design interviews. It is not a replacement for the chapter notes. It is a compact layer for:
- last-minute revision
- mock interview preparation
- trade-off recall
- common architecture patterns

---

## 1. Default Interview Flow
Use this order in most interviews:
1. clarify requirements
2. estimate scale
3. identify core entities and APIs
4. draw high-level architecture
5. explain write path
6. explain read path
7. deep dive into the hardest bottlenecks
8. discuss failures, trade-offs, and future scale

---

## 2. Questions to Ask Early
- What are the core features?
- What is the expected scale: DAU, QPS, storage, peak traffic?
- Is the product global or single-region?
- What are the latency requirements?
- Is consistency strict or can it be eventual?
- Which parts are read-heavy and which are write-heavy?
- Are there cost constraints?
- What failures must the system survive?

---

## 3. Core Building Blocks
### Networking
- DNS resolves domain names to IP addresses.
- IP identifies the machine.
- Port identifies the service on that machine.
- TCP provides reliable ordered delivery.
- UDP provides lower-overhead best-effort delivery.
- HTTPS is HTTP over TLS.

### Traffic Handling
- load balancer distributes requests
- reverse proxy terminates TLS and routes traffic
- CDN caches content near users
- API gateway centralizes cross-cutting concerns such as auth, rate limiting, and routing

### Data and Storage
- cache reduces repeated reads
- database stores durable data
- replica improves availability and read scale
- shard splits data across machines
- queue decouples producers and consumers
- stream stores ordered events

---

## 4. Most Common Trade-Offs
- consistency vs availability
- latency vs durability
- reads vs writes
- eager computation vs lazy computation
- simplicity vs flexibility
- cost vs performance
- global correctness vs local speed

---

## 5. Common Architecture Patterns
### Read-Heavy Systems
Use:
- cache
- replicas
- CDN
- precomputation

Examples:
- feeds
- URL redirects
- autocomplete

### Write-Heavy Systems
Use:
- partitioning
- batching
- append-only logs
- async processing

Examples:
- metrics pipelines
- event ingestion
- chat history writes

### Real-Time Systems
Use:
- WebSocket or streaming protocols
- low-latency storage
- fan-out pipelines
- presence tracking

Examples:
- chat
- live collaboration
- multiplayer systems

### Media Systems
Use:
- object storage
- asynchronous workers
- chunked uploads
- CDN delivery

Examples:
- YouTube
- Google Drive

---

## 6. Failure Thinking
Always ask:
- What if one application server fails?
- What if the cache is down?
- What if one database replica is stale?
- What if one region fails?
- What if retries create duplicates?
- What if traffic spikes 10x?

Common mitigation patterns:
- retries with backoff
- idempotency keys
- circuit breakers
- replication
- leader election
- health checks
- dead-letter queues

---

## 7. What to Deep Dive Into
Pick the part with the highest technical risk:
- cache invalidation
- rate limiting correctness
- replication and consistency
- hot key handling
- feed fan-out
- message ordering
- search ranking freshness
- transcoding pipeline coordination
- file synchronization conflicts
- geospatial indexing

---

## 8. Common Interview Mistakes
- starting implementation before clarifying scope
- naming products or tools before explaining roles
- ignoring scale
- ignoring bottlenecks
- describing only happy-path behavior
- avoiding trade-offs
- over-engineering for tiny scale
- failing to summarize at the end

---

## 9. Red Flags and Strong Signals
### Red Flags
- “I would just shard it” without shard-key discussion
- “Use cache” without invalidation plan
- “Use eventual consistency” without user impact explanation
- “Use microservices” without reason

### Strong Signals
- clear assumptions
- concrete estimates
- clean read/write paths
- realistic failure analysis
- explicit trade-off discussion
- simple but scalable architecture

---

## 10. Last-Minute Revision List
Before an interview, be able to explain:
- DNS, IP, ports, TCP, HTTP, TLS
- caching and cache invalidation
- replication and sharding
- CAP and quorum basics
- queues and async processing
- load balancers and CDNs
- stateless vs stateful services
- rate limiting
- consistent hashing
- feed fan-out
- message ordering and WebSocket
- search autocomplete trie
- object storage and chunked upload
- delta sync and conflict resolution
- geohash and spatial indexing

---

## 11. Final Advice
System design interviews usually reward clarity more than complexity.

A strong answer is usually:
- correct enough
- simple enough
- scalable enough
- explicit about trade-offs
