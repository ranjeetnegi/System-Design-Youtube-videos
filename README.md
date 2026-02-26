# YouTube Video Series: 368 Topics (Intern → Staff-Level)

**Format:** 4–5 min per video · **Audience:** Absolute beginner (intern) to Staff-level (L6) · **Goal:** Complete system design mastery

**Total topics:** 368

---

## How to Use This List

- **Order:** Watch Parts in sequence for a clear learning path.
- **Levels:**  
  - **B** = Beginner / Intern (0–1 year)  
  - **I** = Intermediate (1–2 years)  
  - **S** = Senior / Staff track (2+ years, building toward L6)

---

# PART 0: Absolute Basics (For Complete Beginners / Interns)

## 0.1 How Software & the Internet Work

| # | Topic | Level |
|---|--------|--------|
| 1 | What is a "system" in software? | B |
| 2 | What is a server? What is a client? | B |
| 3 | What happens when you type a URL and press Enter? | B |
| 4 | What is a request and a response? | B |
| 5 | What is an API? (Plain English) | B |
| 6 | Frontend vs backend: what's the difference? | B |
| 7 | What is a database and why do we need it? | B |
| 8 | What is "scale" and why does it matter? | B |
| 9 | What is latency? Why does speed matter? | B |
| 10 | What is a "service" or "microservice"? (High level) | B |

## 0.2 Numbers Every Beginner Should Know

| # | Topic | Level |
|---|--------|--------|
| 11 | Orders of magnitude: 1K, 1M, 1B | B |
| 12 | How to estimate: users, requests per second | B |
| 13 | What is "QPS" or "throughput"? | B |
| 14 | What does "99.9% availability" mean? | B |
| 15 | How much can one server handle? (Rough numbers) | B |

## 0.3 Basic CS Concepts (No Deep Theory)

| # | Topic | Level |
|---|--------|--------|
| 16 | What is a process and a thread? | B |
| 17 | What is RAM (memory) and why it matters for servers | B |
| 18 | What is CPU and when does it become a bottleneck? | B |
| 19 | What is disk I/O and why it's slow | B |
| 20 | What is a hash function? (Simple intuition) | B |
| 21 | What is caching? (Everyday analogy) | B |
| 22 | What is "state" vs "stateless"? | B |
| 23 | What is idempotency? (One simple example) | B |
| 24 | What is a queue? (Real-world analogy) | B |
| 25 | What is synchronous vs asynchronous? | B |

## 0.4 Day-One Essentials (Every Intern Must Know)

| # | Topic | Level |
|---|--------|--------|
| 345 | What are foreign keys and JOINs? | B |
| 346 | SQL injection: The attack every developer must know | B |
| 347 | What is CORS? (Cross-Origin Resource Sharing) | B |
| 348 | What is CI/CD? (Code to production) | B |
| 349 | What is Docker? (Containers explained) | B |

---

# PART 1: System Design Framework (First Pass)

*Placed early so students have a mental model BEFORE learning individual building blocks.*

## 1.1 Thinking in Systems

| # | Topic | Level |
|---|--------|--------|
| 115 | What is system design? (Interview and real world) | B |
| 116 | Requirements first: Why clarify before designing | B |
| 117 | Functional vs non-functional requirements | B |
| 118 | Capacity estimation: Users, QPS, storage | I |
| 119 | Back-of-the-envelope math (orders of magnitude) | I |
| 120 | What breaks first at 2x, 10x, 100x scale? | I |
| 121 | Single point of failure: How to avoid it | I |
| 122 | Vertical vs horizontal scaling: When which? | I |
| 123 | When to split a monolith into services | I |
| 124 | Request flow: From user to DB and back | I |
| 125 | Sync vs async: When to use which in design | I |
| 126 | Queue vs direct RPC: When to use which? | I |
| 127 | Design for backpressure (high-level) | I |

---

# PART 2: Networking Fundamentals

## 2.1 HTTP & Web Basics

| # | Topic | Level |
|---|--------|--------|
| 26 | HTTP: What is a method (GET, POST, PUT, DELETE)? | B |
| 27 | HTTP status codes: 200, 404, 500 (what they mean) | B |
| 28 | What are HTTP headers and why they matter | B |
| 29 | What is REST? (Simple explanation) | B |
| 30 | HTTP vs HTTPS: What is the difference? | B |
| 31 | What is TLS/SSL? (Encryption in transit) | I |
| 32 | What is a reverse proxy? | I |
| 33 | What is a load balancer? (Basic idea) | I |
| 321 | What are load balancing algorithms? | I |
| 322 | L4 vs L7 load balancing | I |
| 323 | What are sticky sessions? | I |
| 34 | What is DNS and how does it work? | B |
| 35 | What is a CDN? (Content delivery in plain English) | I |
| 350 | HTTP/2 and HTTP/3: What changed and why | I |

## 2.2 TCP/IP & Lower Layers

| # | Topic | Level |
|---|--------|--------|
| 36 | TCP vs UDP: When to use which? | I |
| 37 | What is a socket and a connection? | I |
| 38 | What is connection pooling and why use it? | I |
| 39 | What is the OSI model? (7 layers overview) | I |
| 40 | What is bandwidth vs latency? | I |
| 41 | What is Geo-DNS and when is it used? | S |
| 42 | What is Anycast routing? | S |

---

# PART 3: Data & Databases (Beginner to Intermediate)

## 3.1 Database Basics

| # | Topic | Level |
|---|--------|--------|
| 43 | What is a relational database? (Tables, rows, columns) | B |
| 44 | What is SQL? (SELECT, INSERT, UPDATE, DELETE) | B |
| 45 | What is a primary key and an index? | B |
| 46 | Why do we need indexes? Trade-offs | I |
| 47 | What is a transaction? (ACID in plain English) | I |
| 48 | ACID: Atomicity explained | I |
| 49 | ACID: Consistency explained | I |
| 50 | ACID: Isolation and Durability | I |
| 51 | When does a single database "run out"? | I |
| 52 | SQL vs NoSQL: When to use which? | I |
| 53 | What is a key-value store? (Redis, DynamoDB style) | I |
| 54 | What is a document store? (MongoDB style) | I |
| 55 | What is a search engine? (Elasticsearch style) | I |
| 324 | What is a wide-column store? (Cassandra, HBase) | I |
| 325 | What is a graph database? (Neo4j, Neptune) | I |
| 56 | OLTP vs OLAP: What's the difference? | I |
| 57 | What is read replica? Why separate read and write? | I |
| 58 | What is database replication? (Leader-follower) | I |
| 59 | Sync vs async replication: Trade-offs | I |
| 60 | What is replication lag and why it matters? | I |

## 3.2 Sharding, Partitioning & IDs

| # | Topic | Level |
|---|--------|--------|
| 61 | What is sharding? (Splitting data across DBs) | I |
| 62 | What is a partition key? How to choose one? | I |
| 63 | Hash-based sharding explained | I |
| 64 | Range-based sharding explained | I |
| 65 | What is consistent hashing? (Simple version) | I |
| 66 | What are hot partitions and hot keys? | I |
| 67 | How to handle hot keys: Caching, splitting, salting | S |
| 68 | What is data skew and how to avoid it? | S |
| 69 | Denormalization: When and why? | I |
| 70 | Multi-tenant data: How to isolate? | I |
| 326 | How to generate unique IDs at scale | I |
| 327 | Snowflake IDs: Twitter's solution to unique IDs | I |

## 3.3 Query Optimization & Data Lifecycle

| # | Topic | Level |
|---|--------|--------|
| 351 | The N+1 query problem (and how to fix it) | I |
| 352 | Materialized views: Pre-computed query results | I |
| 71 | Block vs file vs object storage (high level) | I |
| 72 | What is object storage? (S3-style) | I |
| 73 | When to use object storage vs database | I |
| 74 | Soft delete vs hard delete | I |
| 75 | Data retention and archival (basics) | I |
| 76 | Schema migration: Adding a column safely | I |
| 77 | What is backfill and when do you need it? | I |
| 78 | Data compression: Why and when (overview) | I |
| 353 | Database backup and recovery: Don't lose your data | I |

---

# PART 4: Caching (Full Arc)

## 4.1 Caching Concepts

| # | Topic | Level |
|---|--------|--------|
| 79 | Why cache? Latency and load reduction | B |
| 80 | Where to cache: Client, edge, server, DB layer | I |
| 81 | Cache-aside pattern (lazy loading) | I |
| 82 | Write-through cache | I |
| 83 | Write-back and write-around (when used) | I |
| 84 | Cache invalidation: Why it's hard | I |
| 85 | TTL (time-to-live): Simple invalidation | I |
| 86 | Invalidate on write: When and how | I |
| 87 | Cache eviction: LRU explained | I |
| 88 | Cache eviction: LFU and other policies | I |
| 89 | What is cache stampede? (Thundering herd) | I |
| 90 | How to prevent cache stampede: Locking, jitter | I |
| 91 | Hot keys in cache: Replication, local cache | S |
| 92 | How to measure cache: Hit rate, miss rate | I |
| 93 | When caching hurts: Stale data, complexity | I |
| 94 | Distributed cache: Redis cluster basics | I |
| 95 | CDN: How it works and when to use it | I |
| 328 | Cache vs read replica: When to use which? | I |

---

# PART 5: APIs, Communication & Security

## 5.1 API Design

| # | Topic | Level |
|---|--------|--------|
| 96 | What is an API endpoint? (REST basics) | B |
| 97 | API versioning: Why and how (v1, v2) | I |
| 98 | API design: Idempotency for write APIs | I |
| 99 | How to evolve APIs without breaking clients | I |
| 100 | Request IDs and tracing (why they matter) | I |
| 303 | gRPC vs REST: When to use which | I |
| 304 | GraphQL: When it fits | I |
| 305 | Webhook vs polling | I |
| 329 | Data serialization: JSON vs Protobuf vs Avro | I |

## 5.2 Authentication & Authorization

| # | Topic | Level |
|---|--------|--------|
| 101 | Authentication vs authorization (difference) | B |
| 102 | Session-based auth: How it works | I |
| 103 | Token-based auth: How it works | I |
| 104 | What is JWT? (Structure and use) | I |
| 105 | OAuth 2.0: High-level flow | I |
| 106 | When to use session vs token | I |
| 354 | RBAC: Role-Based Access Control | I |
| 107 | mTLS: Service-to-service auth | S |

## 5.3 Rate Limiting & Protection

| # | Topic | Level |
|---|--------|--------|
| 108 | Why rate limiting? Overload and abuse | B |
| 109 | Token bucket algorithm (simple) | I |
| 110 | Sliding window and fixed window | I |
| 111 | Rate limiting per user vs per IP vs global | I |
| 112 | How to rate limit without hurting good users | I |
| 113 | Distributed rate limiting: The challenge | S |
| 114 | Protecting downstream services from spikes | I |

---

# PART 6: Distributed Systems (Core Concepts)

## 6.1 Consistency & CAP

| # | Topic | Level |
|---|--------|--------|
| 128 | What is consistency in distributed systems? | I |
| 129 | Strong consistency vs eventual consistency | I |
| 130 | CAP theorem: What it says (simple) | I |
| 131 | CAP: Why you can't have all three | I |
| 132 | CP vs AP: How to choose in practice | S |
| 133 | Consistency models: Linearizability, causal | S |
| 316 | Read-your-writes consistency | S |
| 317 | Monotonic reads and consistent prefix | S |
| 134 | Network partition: What happens when link fails? | I |
| 135 | Split-brain: What it is and why it's dangerous | S |
| 136 | Quorum: Why majority matters | I |
| 137 | BASE (Basically Available, Soft state, Eventual) | I |

## 6.2 Replication & Consensus

| # | Topic | Level |
|---|--------|--------|
| 138 | Leader-follower replication (detailed) | I |
| 139 | Multi-leader replication: When and problems | S |
| 140 | What is consensus? (Agreeing in distributed system) | I |
| 141 | Paxos: Idea (not full proof) | S |
| 142 | Raft: Leader election (simple) | I |
| 143 | Raft: Log replication (simple) | I |
| 144 | When to use Raft/Paxos (etcd, ZooKeeper) | S |
| 145 | Leader election: Why and how | I |
| 146 | Heartbeats: Detecting failures | I |
| 147 | Gossip protocol: Epidemic spread of info | S |
| 148 | Clock sync in distributed systems: The problem | S |
| 149 | Logical clocks: Lamport timestamps | S |
| 150 | Vector clocks: Detecting concurrent events | S |

## 6.3 Transactions, Messaging & Concurrency

| # | Topic | Level |
|---|--------|--------|
| 151 | Distributed transaction: Why it's hard | I |
| 152 | Two-phase commit (2PC): How it works | I |
| 153 | Why 2PC is painful in practice | I |
| 154 | SAGA pattern: Idea and when to use | I |
| 155 | SAGA: Choreography vs orchestration | S |
| 156 | Compensation in SAGA: Rolling back without 2PC | S |
| 157 | Outbox pattern: Reliable event publishing | I |
| 158 | Inbox / idempotent consumer: Avoiding duplicates | I |
| 159 | Delivery semantics: At-most-once, at-least-once | I |
| 160 | "Exactly-once" is at-least-once + idempotency | I |
| 161 | Message queue vs log (Kafka-style): Difference | I |
| 162 | When to use a queue vs a log vs a stream | I |
| 163 | Change Data Capture (CDC): What and why | I |
| 164 | Retries and exponential backoff | I |
| 165 | Circuit breaker: Stop cascading failures | I |
| 355 | Dead letter queue: Where failed messages go | I |
| 330 | Optimistic vs pessimistic locking | I |
| 331 | Compare-and-Swap (CAS): Lock-free concurrency | S |

---

# PART 7: Failure, Reliability & Availability

## 7.1 Failure Modes

| # | Topic | Level |
|---|--------|--------|
| 166 | Availability vs reliability vs durability | I |
| 167 | Fault tolerance vs high availability | I |
| 168 | Partial failure: Why things fail in pieces | I |
| 169 | Cascading failure: How one failure spreads | I |
| 170 | Timeouts: Why set them and what value? | I |
| 171 | Health checks: Liveness vs readiness | I |
| 172 | Graceful degradation: What to shed first | S |
| 173 | Load shedding: Dropping work to save the system | S |
| 174 | Deadlock and how to avoid it (distributed) | I |
| 175 | Distributed locks: When and how (Redis, etcd) | S |
| 309 | Bulkheads: Isolating failure | S |
| 310 | Chaos engineering: Why and how (intro) | S |

## 7.2 Multi-Region & Disaster Recovery

| # | Topic | Level |
|---|--------|--------|
| 176 | Why multi-region? Latency and availability | I |
| 177 | Active-passive vs active-active (high level) | I |
| 178 | Cross-region replication: Challenges | S |
| 179 | Data residency and compliance (GDPR-style) | I |
| 180 | Failover and failback: Basics | I |
| 356 | RPO and RTO: The numbers that define disaster recovery | I |

---

# PART 8: Architecture Patterns & Styles

## 8.1 Service Architecture

| # | Topic | Level |
|---|--------|--------|
| 181 | Monolith: When it's fine | B |
| 182 | Microservices: What and when to split | I |
| 360 | Strangler fig pattern: Migrating away from a monolith | I |
| 362 | Database per service: Should each service own its data? | I |
| 183 | Event-driven architecture: Overview | I |
| 184 | Serverless: When and trade-offs | I |
| 185 | API gateway: What it does | I |
| 186 | API gateway: Auth, rate limit, route, proxy | I |
| 361 | Backend for Frontend (BFF): One API per client | I |
| 187 | Service mesh: What problem it solves | S |
| 359 | Sidecar pattern: A helper for every service | S |
| 332 | Service discovery: How services find each other | I |

## 8.2 Real-Time & Data Flow

| # | Topic | Level |
|---|--------|--------|
| 188 | WebSockets: When to use for real-time | I |
| 189 | Long polling vs WebSocket vs SSE | I |
| 190 | Server-Sent Events (SSE): One-way push | I |
| 191 | Cursor-based pagination vs offset | I |
| 192 | Why offset pagination breaks at scale | I |
| 193 | Event sourcing (high-level idea) | S |
| 194 | CQRS: Command Query Responsibility Segregation | S |

## 8.3 Infrastructure & Deployment

| # | Topic | Level |
|---|--------|--------|
| 357 | Kubernetes: Container orchestration basics | I |
| 358 | Auto-scaling: Scaling up and down automatically | I |

---

# PART 9: Staff-Level Mindset & Interview

## 9.1 What Staff-Level Means

| # | Topic | Level |
|---|--------|--------|
| 195 | L5 vs L6: What changes in system design? | S |
| 196 | Scope: Not assigned, created by you | S |
| 197 | Impact: Outcomes, not output | S |
| 198 | Ownership: Beyond your code | S |
| 199 | Trade-offs: Making them explicit | S |
| 200 | Designing under ambiguity | S |
| 201 | APIs as long-term contracts | S |
| 202 | When to version, when to split services | S |
| 203 | Cost as a first-class constraint | S |
| 204 | What to build vs what not to build | S |
| 363 | Writing RFCs and design documents | S |
| 364 | Managing technical debt at Staff level | S |

## 9.2 Interview, Communication & Practice

| # | Topic | Level |
|---|--------|--------|
| 205 | How Staff system design interviews are evaluated | S |
| 206 | Driving the conversation (interview leadership) | S |
| 207 | The 4-phase flow: Understand, High-level, Deep, Wrap-up | S |
| 208 | Stating assumptions out loud | S |
| 209 | Defending your design under challenge | S |
| 210 | Time management in a 45-min design | S |
| 211 | Phrases that signal Staff-level thinking | S |
| 212 | When to go deep vs when to stay high-level | S |
| 333 | How to practice system design (action plan) | I |

---

# PART 10: Design Problems (Broken Into Topics)

*Each design can be 3–8 short videos: problem, requirements, high-level, key components, scale, failure, trade-offs.*

## 10.1 Classic Problems (Senior-Level)

| # | Topic | Level |
|---|--------|--------|
| 213 | URL shortener: Problem and requirements | I |
| 214 | URL shortener: High-level design and flow | I |
| 215 | URL shortener: Scale and bottlenecks | I |
| 216 | Rate limiter: Problem and algorithms | I |
| 217 | Rate limiter: Single-region design | I |
| 218 | Distributed cache: What and when | I |
| 219 | Distributed cache: Sharding and consistent hashing | I |
| 220 | Object storage: Durability and API | I |
| 221 | Object storage: Scaling and cost | I |
| 222 | Notification system: Channels and delivery | I |
| 223 | Notification system: Queue and scaling | I |
| 224 | Authentication system: Flows and tokens | I |
| 225 | Search system: Index and query path | I |
| 226 | Metrics pipeline: Ingestion and storage | I |
| 227 | Background job queue: Enqueue, workers, retries | I |
| 228 | Payment flow: ACID and idempotency | I |
| 229 | API gateway: Pipeline and components | I |
| 230 | Real-time chat: WebSocket and persistence | I |
| 231 | Configuration management: Push vs pull | I |
| 334 | Unique ID generator: Design and trade-offs | I |
| 335 | Typeahead / autocomplete: Design | I |
| 336 | Booking / ticketing system: Design | I |

## 10.2 Staff-Level Problems (Deep Dives)

| # | Topic | Level |
|---|--------|--------|
| 232 | Global rate limiter: Distributed counting | S |
| 233 | Global rate limiter: Consistency vs latency | S |
| 234 | Distributed cache at scale: Multi-region | S |
| 235 | News feed: Fan-out, ranking, scale | S |
| 236 | News feed: Backpressure and load shedding | S |
| 237 | Real-time collaboration: CRDTs and ordering | S |
| 238 | Messaging platform: Delivery and presence | S |
| 239 | Messaging platform: Scale and WebSockets | S |
| 240 | Metrics/observability: Cardinality and cost | S |
| 241 | Config and feature flags: Propagation and safety | S |
| 242 | API gateway at scale: Rate limit layers | S |
| 243 | API gateway: Backpressure and failover | S |
| 244 | Search system: Sharding and indexing | S |
| 245 | Recommendation system: Data and ranking | S |
| 246 | Notification fan-out: Scale and dedup | S |
| 247 | Auth & authorization: mTLS and revocation | S |
| 248 | Distributed scheduler: Heartbeats and guarantees | S |
| 249 | A/B testing: Assignment and consistency | S |
| 250 | Log aggregation: Tiering and compression | S |
| 251 | Payment system: SAGA and compliance | S |
| 252 | Media pipeline: Storage and transcoding | S |
| 337 | File sync service (Dropbox): Design | S |
| 338 | Video streaming platform: Design | S |
| 339 | Ride-sharing service: Design | S |
| 340 | Web crawler: Design | S |
| 341 | Proximity service: Design | S |

---

# PART 11: Deep Dives (One Concept = One Video)

## 11.1 Data & Storage Deep Dives

| # | Topic | Level |
|---|--------|--------|
| 253 | B-tree index: Why databases use it | I |
| 254 | Secondary indexes: Cost and use | I |
| 255 | Write-ahead log (WAL): What and why | I |
| 256 | MVCC: Multi-version concurrency control | S |
| 257 | Database connection pooling in practice | I |
| 258 | When to use NewSQL (Spanner, CockroachDB) | S |
| 259 | Time-series DB: Why different (Gorilla compression) | I |
| 260 | Inverted index: How search engines work | I |
| 342 | LSM Tree: How write-heavy databases work | I |
| 343 | Bloom filter: The fastest way to say "probably yes" | I |
| 344 | Geospatial indexing: Finding nearby things fast | I |

## 11.2 Caching & CDN Deep Dives

| # | Topic | Level |
|---|--------|--------|
| 261 | Cache key design: What to include | I |
| 262 | Cache poisoning: How to prevent | I |
| 263 | Stale reads: When acceptable | I |
| 264 | Edge caching: Cache control headers | I |
| 265 | Redis persistence: RDB vs AOF | I |
| 266 | Redis cluster: Slots and routing | I |

## 11.3 Reliability & Ops

| # | Topic | Level |
|---|--------|--------|
| 267 | Blue-green vs canary deployment | I |
| 268 | Feature flags: Safe rollout | I |
| 269 | Runbooks and incident response | I |
| 365 | Post-mortems: Blameless incident review | I |
| 270 | Observability: Logs, metrics, traces | I |
| 366 | Structured logging: Logs that machines can read | I |
| 367 | Alerting strategy: What to alert on (and what not to) | I |
| 271 | SLO, SLI, error budget (basics) | I |
| 272 | Capacity planning: How to do it | I |
| 273 | Cost modeling: Major drivers | I |
| 274 | Migration without downtime: Strategies | S |
| 275 | Rollback: When and how | I |

## 11.4 Security & Compliance

| # | Topic | Level |
|---|--------|--------|
| 276 | Principle of least privilege | I |
| 277 | Secrets management: Don't hardcode | I |
| 278 | Encryption at rest vs in transit | I |
| 279 | Audit logging: What and why | I |
| 280 | Data deletion and right to erasure | I |

## 11.5 Kafka & Event Systems

| # | Topic | Level |
|---|--------|--------|
| 281 | Kafka: Topic, partition, offset | I |
| 282 | Kafka consumer groups | I |
| 283 | Kafka: Ordering and partitioning key | I |
| 284 | Kafka retention and compaction | I |
| 285 | Kafka exactly-once: Limits | I |
| 286 | When Kafka is overkill | I |
| 287 | Pub/Sub vs Kafka: When which? | I |

## 11.6 Data Pipelines

| # | Topic | Level |
|---|--------|--------|
| 368 | Data pipelines: ETL and ELT basics | I |

## 11.7 More Staff-Level Nuances

| # | Topic | Level |
|---|--------|--------|
| 288 | Replication lag: Monitoring and handling | S |
| 289 | Cross-region consistency: The real trade-offs | S |
| 290 | When "eventually consistent" is not OK | S |
| 291 | Designing for partial failure (checklist) | S |
| 292 | Organizational scaling: APIs and ownership | S |
| 293 | Platform vs product team mindset | S |
| 294 | Deprecation and migration of APIs | S |
| 295 | What interviewers probe at Staff level | S |
| 296 | Rejecting the wrong solution (out loud) | S |
| 297 | Scope creep in interviews: How to avoid | S |
| 298 | Drawing and explaining in 2 minutes | S |
| 299 | End of interview: What to say | S |
| 300 | Learning path: From beginner to Staff (roadmap) | S |

---

# PART 12: Bonus Topics

| # | Topic | Level |
|---|--------|--------|
| 301 | Three-phase commit (3PC): Why rarely used | S |
| 302 | OpenID Connect vs OAuth (identity layer) | I |
| 306 | Idempotency keys: Client-generated | I |
| 307 | Deduplication windows in event systems | I |
| 308 | Redundant requests: When to dedupe | I |
| 311 | Feature flags and experimentation (A/B) | I |
| 312 | Data lineage and governance (intro) | I |
| 313 | Multi-region failover: Decision process | S |
| 314 | Cold start: Serverless and caches | I |
| 315 | Warm pool and pre-warming | I |
| 318 | Hybrid Logical Clocks (HLC) (idea) | S |
| 319 | CRDT: Conflict-free replicated data types (intro) | S |
| 320 | Event sourcing vs event-driven (difference) | S |

---

## Summary

| Part | Topics | Count |
|------|--------|-------|
| Part 0: Absolute basics + intern essentials | 1–25, 345–349 | 30 |
| Part 1: System design framework | 115–127 | 13 |
| Part 2: Networking | 26–42, 321–323, 350 | 21 |
| Part 3: Data & databases | 43–78, 324–327, 351–353 | 43 |
| Part 4: Caching | 79–95, 328 | 18 |
| Part 5: APIs & security | 96–114, 303–305, 329, 354 | 24 |
| Part 6: Distributed systems | 128–165, 316–317, 330–331, 355 | 43 |
| Part 7: Failure & availability | 166–180, 309–310, 356 | 18 |
| Part 8: Architecture patterns | 181–194, 332, 357–362 | 21 |
| Part 9: Staff mindset & interview | 195–212, 333, 363–364 | 21 |
| Part 10: Design problems | 213–252, 334–341 | 48 |
| Part 11: Deep dives | 253–300, 342–344, 365–368 | 55 |
| Part 12: Bonus | 301–302, 306–308, 311–315, 318–320 | 13 |

**Total: 368 topics** (each has a script in `Video_Scripts/`).

---

## New Topics Added (345–368)

| # | Topic | Placed In |
|---|--------|-----------|
| 345 | What are foreign keys and JOINs? | Part 0: Intern essentials |
| 346 | SQL injection: The attack every developer must know | Part 0: Intern essentials |
| 347 | What is CORS? (Cross-Origin Resource Sharing) | Part 0: Intern essentials |
| 348 | What is CI/CD? (Code to production) | Part 0: Intern essentials |
| 349 | What is Docker? (Containers explained) | Part 0: Intern essentials |
| 350 | HTTP/2 and HTTP/3: What changed and why | Part 2: Networking |
| 351 | The N+1 query problem (and how to fix it) | Part 3: Databases |
| 352 | Materialized views: Pre-computed query results | Part 3: Databases |
| 353 | Database backup and recovery | Part 3: Databases |
| 354 | RBAC: Role-Based Access Control | Part 5: Security |
| 355 | Dead letter queue: Where failed messages go | Part 6: Distributed Systems |
| 356 | RPO and RTO: Disaster recovery numbers | Part 7: Reliability |
| 357 | Kubernetes: Container orchestration basics | Part 8: Architecture |
| 358 | Auto-scaling: Scaling up and down automatically | Part 8: Architecture |
| 359 | Sidecar pattern: A helper for every service | Part 8: Architecture |
| 360 | Strangler fig pattern: Migrating from a monolith | Part 8: Architecture |
| 361 | Backend for Frontend (BFF): One API per client | Part 8: Architecture |
| 362 | Database per service: Should each service own its data? | Part 8: Architecture |
| 363 | Writing RFCs and design documents | Part 9: Staff Mindset |
| 364 | Managing technical debt at Staff level | Part 9: Staff Mindset |
| 365 | Post-mortems: Blameless incident review | Part 11: Reliability & Ops |
| 366 | Structured logging: Logs that machines can read | Part 11: Reliability & Ops |
| 367 | Alerting strategy: What to alert on | Part 11: Reliability & Ops |
| 368 | Data pipelines: ETL and ELT basics | Part 11: Data Pipelines |

---

## Suggested Release Order

1. **Phase 1 (Intern/Beginner):** Part 0 (all sections) → Part 1 (framework) → Part 2.1 → Part 3.1–3.2 → Part 4.1 → Part 5.1–5.2  
2. **Phase 2 (Intermediate):** Part 2.2, Part 3.3, Part 5.3, Part 6.1–6.2, Part 7, Part 8  
3. **Phase 3 (Senior/Staff):** Part 6.3, Part 9, Part 10, Part 11, Part 12  

You can also release by "tracks" (e.g. "Caching track", "Distributed systems track") for viewers who want to go deep in one area.
