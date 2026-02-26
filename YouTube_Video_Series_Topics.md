# System Design YouTube Series: 368 Topics (Intern → Staff-Level)

**Format:** 4–5 min per video · **Audience:** Absolute beginner (intern) to Staff-level (L6) · **Goal:** Complete system design mastery

**Total topics:** 368 · Topics are numbered **001 → 368 in strictly increasing complexity**.

---

## How to Use This Series

- **Watch in order.** Topic 001 assumes zero knowledge. Topic 368 is Staff-level.
- **Levels:**  
  - **B** = Beginner / Intern (0–1 year)  
  - **I** = Intermediate (1–2 years)  
  - **S** = Senior / Staff track (2+ years, building toward L6)
- **Each video:** ~4–5 minutes with analogy, diagram, real-world example, disaster story, and recap.

---

# PART 1: Absolute Basics (Topics 001–030)

*Zero assumptions. Start here if you're new to software.*

## 1.1 How Software & the Internet Work (001–010)

| # | Topic | Level |
|---|--------|--------|
| 001 | What is a "system" in software? | B |
| 002 | What is a server? What is a client? | B |
| 003 | What happens when you type a URL and press Enter? | B |
| 004 | What is a request and a response? | B |
| 005 | What is an API? (Plain English) | B |
| 006 | Frontend vs backend: what's the difference? | B |
| 007 | What is a database and why do we need it? | B |
| 008 | What is "scale" and why does it matter? | B |
| 009 | What is latency? Why does speed matter? | B |
| 010 | What is a "service" or "microservice"? (High level) | B |

## 1.2 Numbers Every Beginner Should Know (011–015)

| # | Topic | Level |
|---|--------|--------|
| 011 | Orders of magnitude: 1K, 1M, 1B | B |
| 012 | How to estimate: users, requests per second | B |
| 013 | What is "QPS" or "throughput"? | B |
| 014 | What does "99.9% availability" mean? | B |
| 015 | How much can one server handle? (Rough numbers) | B |

## 1.3 Basic CS Concepts (016–025)

| # | Topic | Level |
|---|--------|--------|
| 016 | What is a process and a thread? | B |
| 017 | What is RAM and why it matters for servers | B |
| 018 | What is CPU and when does it become a bottleneck? | B |
| 019 | What is disk I/O and why it's slow | B |
| 020 | What is a hash function? (Simple intuition) | B |
| 021 | What is caching? (Everyday analogy) | B |
| 022 | What is "state" vs "stateless"? | B |
| 023 | What is idempotency? (One simple example) | B |
| 024 | What is a queue? (Real-world analogy) | B |
| 025 | What is synchronous vs asynchronous? | B |

## 1.4 Day-One Intern Essentials (026–030)

| # | Topic | Level |
|---|--------|--------|
| 026 | What are foreign keys and JOINs? | B |
| 027 | SQL injection: The attack every developer must know | B |
| 028 | What is CORS? (Cross-Origin Resource Sharing) | B |
| 029 | What is CI/CD? (Code to production) | B |
| 030 | What is Docker? (Containers explained) | B |

---

# PART 2: System Design Framework (Topics 031–043)

*Learn how to think about design BEFORE diving into building blocks.*

| # | Topic | Level |
|---|--------|--------|
| 031 | What is system design? (Interview and real world) | B |
| 032 | Requirements first: Why clarify before designing | B |
| 033 | Functional vs non-functional requirements | B |
| 034 | Capacity estimation: Users, QPS, storage | I |
| 035 | Back-of-the-envelope math (orders of magnitude) | I |
| 036 | What breaks first at 2x, 10x, 100x scale? | I |
| 037 | Single point of failure: How to avoid it | I |
| 038 | Vertical vs horizontal scaling: When which? | I |
| 039 | When to split a monolith into services | I |
| 040 | Request flow: From user to DB and back | I |
| 041 | Sync vs async: When to use which in design | I |
| 042 | Queue vs direct RPC: When to use which? | I |
| 043 | Design for backpressure (high-level) | I |

---

# PART 3: Networking Fundamentals (Topics 044–064)

## 3.1 HTTP & Web Basics (044–057)

| # | Topic | Level |
|---|--------|--------|
| 044 | HTTP: What is a method (GET, POST, PUT, DELETE)? | B |
| 045 | HTTP status codes: 200, 404, 500 (what they mean) | B |
| 046 | What are HTTP headers and why they matter | B |
| 047 | What is REST? (Simple explanation) | B |
| 048 | HTTP vs HTTPS: What is the difference? | B |
| 049 | What is DNS and how does it work? | B |
| 050 | What is TLS/SSL? (Encryption in transit) | I |
| 051 | What is a reverse proxy? | I |
| 052 | What is a load balancer? (Basic idea) | I |
| 053 | What are load balancing algorithms? | I |
| 054 | L4 vs L7 load balancing | I |
| 055 | What are sticky sessions? | I |
| 056 | What is a CDN? (Content delivery in plain English) | I |
| 057 | HTTP/2 and HTTP/3: What changed and why | I |

## 3.2 TCP/IP & Lower Layers (058–064)

| # | Topic | Level |
|---|--------|--------|
| 058 | TCP vs UDP: When to use which? | I |
| 059 | What is a socket and a connection? | I |
| 060 | What is connection pooling and why use it? | I |
| 061 | What is the OSI model? (7 layers overview) | I |
| 062 | What is bandwidth vs latency? | I |
| 063 | What is Geo-DNS and when is it used? | S |
| 064 | What is Anycast routing? | S |

---

# PART 4: Data & Databases (Topics 065–107)

## 4.1 Database Basics & Types (065–084)

| # | Topic | Level |
|---|--------|--------|
| 065 | What is a relational database? (Tables, rows, columns) | B |
| 066 | What is SQL? (SELECT, INSERT, UPDATE, DELETE) | B |
| 067 | What is a primary key and an index? | B |
| 068 | Why do we need indexes? Trade-offs | I |
| 069 | What is a transaction? (ACID in plain English) | I |
| 070 | ACID: Atomicity explained | I |
| 071 | ACID: Consistency explained | I |
| 072 | ACID: Isolation and Durability | I |
| 073 | When does a single database "run out"? | I |
| 074 | SQL vs NoSQL: When to use which? | I |
| 075 | What is a key-value store? (Redis, DynamoDB) | I |
| 076 | What is a document store? (MongoDB) | I |
| 077 | What is a search engine? (Elasticsearch) | I |
| 078 | What is a wide-column store? (Cassandra, HBase) | I |
| 079 | What is a graph database? (Neo4j, Neptune) | I |
| 080 | OLTP vs OLAP: What's the difference? | I |
| 081 | What is read replica? Why separate read and write? | I |
| 082 | What is database replication? (Leader-follower) | I |
| 083 | Sync vs async replication: Trade-offs | I |
| 084 | What is replication lag and why it matters? | I |

## 4.2 Sharding, Partitioning & IDs (085–096)

| # | Topic | Level |
|---|--------|--------|
| 085 | What is sharding? (Splitting data across DBs) | I |
| 086 | What is a partition key? How to choose one? | I |
| 087 | Hash-based sharding explained | I |
| 088 | Range-based sharding explained | I |
| 089 | What is consistent hashing? (Simple version) | I |
| 090 | What are hot partitions and hot keys? | I |
| 091 | Denormalization: When and why? | I |
| 092 | Multi-tenant data: How to isolate? | I |
| 093 | How to generate unique IDs at scale | I |
| 094 | Snowflake IDs: Twitter's solution to unique IDs | I |
| 095 | How to handle hot keys: Caching, splitting, salting | S |
| 096 | What is data skew and how to avoid it? | S |

## 4.3 Query Optimization & Data Lifecycle (097–107)

| # | Topic | Level |
|---|--------|--------|
| 097 | The N+1 query problem (and how to fix it) | I |
| 098 | Materialized views: Pre-computed query results | I |
| 099 | Block vs file vs object storage (high level) | I |
| 100 | What is object storage? (S3-style) | I |
| 101 | When to use object storage vs database | I |
| 102 | Soft delete vs hard delete | I |
| 103 | Data retention and archival (basics) | I |
| 104 | Schema migration: Adding a column safely | I |
| 105 | What is backfill and when do you need it? | I |
| 106 | Data compression: Why and when (overview) | I |
| 107 | Database backup and recovery: Don't lose your data | I |

---

# PART 5: Caching (Topics 108–125)

| # | Topic | Level |
|---|--------|--------|
| 108 | Why cache? Latency and load reduction | B |
| 109 | Where to cache: Client, edge, server, DB layer | I |
| 110 | Cache-aside pattern (lazy loading) | I |
| 111 | Write-through cache | I |
| 112 | Write-back and write-around (when used) | I |
| 113 | Cache invalidation: Why it's hard | I |
| 114 | TTL (time-to-live): Simple invalidation | I |
| 115 | Invalidate on write: When and how | I |
| 116 | Cache eviction: LRU explained | I |
| 117 | Cache eviction: LFU and other policies | I |
| 118 | What is cache stampede? (Thundering herd) | I |
| 119 | How to prevent cache stampede: Locking, jitter | I |
| 120 | How to measure cache: Hit rate, miss rate | I |
| 121 | When caching hurts: Stale data, complexity | I |
| 122 | Distributed cache: Redis cluster basics | I |
| 123 | CDN: How it works and when to use it | I |
| 124 | Cache vs read replica: When to use which? | I |
| 125 | Hot keys in cache: Replication, local cache | S |

---

# PART 6: APIs, Communication & Security (Topics 126–149)

## 6.1 API Design (126–134)

| # | Topic | Level |
|---|--------|--------|
| 126 | What is an API endpoint? (REST basics) | B |
| 127 | API versioning: Why and how (v1, v2) | I |
| 128 | API design: Idempotency for write APIs | I |
| 129 | How to evolve APIs without breaking clients | I |
| 130 | Request IDs and tracing (why they matter) | I |
| 131 | gRPC vs REST: When to use which | I |
| 132 | GraphQL: When it fits | I |
| 133 | Webhook vs polling | I |
| 134 | Data serialization: JSON vs Protobuf vs Avro | I |

## 6.2 Authentication & Authorization (135–142)

| # | Topic | Level |
|---|--------|--------|
| 135 | Authentication vs authorization (difference) | B |
| 136 | Session-based auth: How it works | I |
| 137 | Token-based auth: How it works | I |
| 138 | What is JWT? (Structure and use) | I |
| 139 | OAuth 2.0: High-level flow | I |
| 140 | When to use session vs token | I |
| 141 | RBAC: Role-Based Access Control | I |
| 142 | mTLS: Service-to-service auth | S |

## 6.3 Rate Limiting & Protection (143–149)

| # | Topic | Level |
|---|--------|--------|
| 143 | Why rate limiting? Overload and abuse | B |
| 144 | Token bucket algorithm (simple) | I |
| 145 | Sliding window and fixed window | I |
| 146 | Rate limiting per user vs per IP vs global | I |
| 147 | How to rate limit without hurting good users | I |
| 148 | Protecting downstream services from spikes | I |
| 149 | Distributed rate limiting: The challenge | S |

---

# PART 7: Distributed Systems (Topics 150–192)

## 7.1 Consistency & CAP (150–161)

| # | Topic | Level |
|---|--------|--------|
| 150 | What is consistency in distributed systems? | I |
| 151 | Strong consistency vs eventual consistency | I |
| 152 | CAP theorem: What it says (simple) | I |
| 153 | CAP: Why you can't have all three | I |
| 154 | Network partition: What happens when link fails? | I |
| 155 | Quorum: Why majority matters | I |
| 156 | BASE (Basically Available, Soft state, Eventual) | I |
| 157 | CP vs AP: How to choose in practice | S |
| 158 | Consistency models: Linearizability, causal | S |
| 159 | Split-brain: What it is and why it's dangerous | S |
| 160 | Read-your-writes consistency | S |
| 161 | Monotonic reads and consistent prefix | S |

## 7.2 Replication & Consensus (162–174)

| # | Topic | Level |
|---|--------|--------|
| 162 | Leader-follower replication (detailed) | I |
| 163 | What is consensus? (Agreeing in distributed system) | I |
| 164 | Raft: Leader election (simple) | I |
| 165 | Raft: Log replication (simple) | I |
| 166 | Leader election: Why and how | I |
| 167 | Heartbeats: Detecting failures | I |
| 168 | Multi-leader replication: When and problems | S |
| 169 | Paxos: Idea (not full proof) | S |
| 170 | When to use Raft/Paxos (etcd, ZooKeeper) | S |
| 171 | Gossip protocol: Epidemic spread of info | S |
| 172 | Clock sync in distributed systems: The problem | S |
| 173 | Logical clocks: Lamport timestamps | S |
| 174 | Vector clocks: Detecting concurrent events | S |

## 7.3 Transactions, Messaging & Concurrency (175–192)

| # | Topic | Level |
|---|--------|--------|
| 175 | Distributed transaction: Why it's hard | I |
| 176 | Two-phase commit (2PC): How it works | I |
| 177 | Why 2PC is painful in practice | I |
| 178 | SAGA pattern: Idea and when to use | I |
| 179 | Outbox pattern: Reliable event publishing | I |
| 180 | Inbox / idempotent consumer: Avoiding duplicates | I |
| 181 | Delivery semantics: At-most-once, at-least-once | I |
| 182 | "Exactly-once" is at-least-once + idempotency | I |
| 183 | Message queue vs log (Kafka-style): Difference | I |
| 184 | When to use a queue vs a log vs a stream | I |
| 185 | Change Data Capture (CDC): What and why | I |
| 186 | Retries and exponential backoff | I |
| 187 | Circuit breaker: Stop cascading failures | I |
| 188 | Dead letter queue: Where failed messages go | I |
| 189 | Optimistic vs pessimistic locking | I |
| 190 | SAGA: Choreography vs orchestration | S |
| 191 | Compensation in SAGA: Rolling back without 2PC | S |
| 192 | Compare-and-Swap (CAS): Lock-free concurrency | S |

---

# PART 8: Failure, Reliability & Availability (Topics 193–210)

## 8.1 Failure Modes (193–204)

| # | Topic | Level |
|---|--------|--------|
| 193 | Availability vs reliability vs durability | I |
| 194 | Fault tolerance vs high availability | I |
| 195 | Partial failure: Why things fail in pieces | I |
| 196 | Cascading failure: How one failure spreads | I |
| 197 | Timeouts: Why set them and what value? | I |
| 198 | Health checks: Liveness vs readiness | I |
| 199 | Deadlock and how to avoid it (distributed) | I |
| 200 | Graceful degradation: What to shed first | S |
| 201 | Load shedding: Dropping work to save the system | S |
| 202 | Distributed locks: When and how (Redis, etcd) | S |
| 203 | Bulkheads: Isolating failure | S |
| 204 | Chaos engineering: Why and how (intro) | S |

## 8.2 Multi-Region & Disaster Recovery (205–210)

| # | Topic | Level |
|---|--------|--------|
| 205 | Why multi-region? Latency and availability | I |
| 206 | Active-passive vs active-active (high level) | I |
| 207 | Data residency and compliance (GDPR-style) | I |
| 208 | Failover and failback: Basics | I |
| 209 | RPO and RTO: The numbers that define disaster recovery | I |
| 210 | Cross-region replication: Challenges | S |

---

# PART 9: Architecture Patterns & Styles (Topics 211–231)

## 9.1 Service Architecture (211–222)

| # | Topic | Level |
|---|--------|--------|
| 211 | Monolith: When it's fine | B |
| 212 | Microservices: What and when to split | I |
| 213 | Strangler fig pattern: Migrating away from a monolith | I |
| 214 | Database per service: Should each service own its data? | I |
| 215 | Event-driven architecture: Overview | I |
| 216 | Serverless: When and trade-offs | I |
| 217 | API gateway: What it does | I |
| 218 | API gateway: Auth, rate limit, route, proxy | I |
| 219 | Backend for Frontend (BFF): One API per client | I |
| 220 | Service discovery: How services find each other | I |
| 221 | Service mesh: What problem it solves | S |
| 222 | Sidecar pattern: A helper for every service | S |

## 9.2 Real-Time, Infrastructure & Data Flow (223–231)

| # | Topic | Level |
|---|--------|--------|
| 223 | WebSockets: When to use for real-time | I |
| 224 | Long polling vs WebSocket vs SSE | I |
| 225 | Server-Sent Events (SSE): One-way push | I |
| 226 | Cursor-based pagination vs offset | I |
| 227 | Why offset pagination breaks at scale | I |
| 228 | Kubernetes: Container orchestration basics | I |
| 229 | Auto-scaling: Scaling up and down automatically | I |
| 230 | Event sourcing (high-level idea) | S |
| 231 | CQRS: Command Query Responsibility Segregation | S |

---

# PART 10: Staff-Level Mindset & Interview (Topics 232–252)

## 10.1 What Staff-Level Means (232–244)

| # | Topic | Level |
|---|--------|--------|
| 232 | How to practice system design (action plan) | I |
| 233 | L5 vs L6: What changes in system design? | S |
| 234 | Scope: Not assigned, created by you | S |
| 235 | Impact: Outcomes, not output | S |
| 236 | Ownership: Beyond your code | S |
| 237 | Trade-offs: Making them explicit | S |
| 238 | Designing under ambiguity | S |
| 239 | APIs as long-term contracts | S |
| 240 | When to version, when to split services | S |
| 241 | Cost as a first-class constraint | S |
| 242 | What to build vs what not to build | S |
| 243 | Writing RFCs and design documents | S |
| 244 | Managing technical debt at Staff level | S |

## 10.2 Interview & Communication (245–252)

| # | Topic | Level |
|---|--------|--------|
| 245 | How Staff system design interviews are evaluated | S |
| 246 | Driving the conversation (interview leadership) | S |
| 247 | The 4-phase flow: Understand, High-level, Deep, Wrap-up | S |
| 248 | Stating assumptions out loud | S |
| 249 | Defending your design under challenge | S |
| 250 | Time management in a 45-min design | S |
| 251 | Phrases that signal Staff-level thinking | S |
| 252 | When to go deep vs when to stay high-level | S |

---

# PART 11: Design Problems (Topics 253–300)

*Each design: problem, requirements, high-level, key components, scale, failure, trade-offs.*

## 11.1 Classic Problems (253–274)

| # | Topic | Level |
|---|--------|--------|
| 253 | URL shortener: Problem and requirements | I |
| 254 | URL shortener: High-level design and flow | I |
| 255 | URL shortener: Scale and bottlenecks | I |
| 256 | Rate limiter: Problem and algorithms | I |
| 257 | Rate limiter: Single-region design | I |
| 258 | Distributed cache: What and when | I |
| 259 | Distributed cache: Sharding and consistent hashing | I |
| 260 | Object storage: Durability and API | I |
| 261 | Object storage: Scaling and cost | I |
| 262 | Notification system: Channels and delivery | I |
| 263 | Notification system: Queue and scaling | I |
| 264 | Authentication system: Flows and tokens | I |
| 265 | Search system: Index and query path | I |
| 266 | Metrics pipeline: Ingestion and storage | I |
| 267 | Background job queue: Enqueue, workers, retries | I |
| 268 | Payment flow: ACID and idempotency | I |
| 269 | API gateway: Pipeline and components | I |
| 270 | Real-time chat: WebSocket and persistence | I |
| 271 | Configuration management: Push vs pull | I |
| 272 | Unique ID generator: Design and trade-offs | I |
| 273 | Typeahead / autocomplete: Design | I |
| 274 | Booking / ticketing system: Design | I |

## 11.2 Staff-Level Problems (275–300)

| # | Topic | Level |
|---|--------|--------|
| 275 | Global rate limiter: Distributed counting | S |
| 276 | Global rate limiter: Consistency vs latency | S |
| 277 | Distributed cache at scale: Multi-region | S |
| 278 | News feed: Fan-out, ranking, scale | S |
| 279 | News feed: Backpressure and load shedding | S |
| 280 | Real-time collaboration: CRDTs and ordering | S |
| 281 | Messaging platform: Delivery and presence | S |
| 282 | Messaging platform: Scale and WebSockets | S |
| 283 | Metrics/observability: Cardinality and cost | S |
| 284 | Config and feature flags: Propagation and safety | S |
| 285 | API gateway at scale: Rate limit layers | S |
| 286 | API gateway: Backpressure and failover | S |
| 287 | Search system: Sharding and indexing | S |
| 288 | Recommendation system: Data and ranking | S |
| 289 | Notification fan-out: Scale and dedup | S |
| 290 | Auth & authorization: mTLS and revocation | S |
| 291 | Distributed scheduler: Heartbeats and guarantees | S |
| 292 | A/B testing: Assignment and consistency | S |
| 293 | Log aggregation: Tiering and compression | S |
| 294 | Payment system: SAGA and compliance | S |
| 295 | Media pipeline: Storage and transcoding | S |
| 296 | File sync service (Dropbox): Design | S |
| 297 | Video streaming platform: Design | S |
| 298 | Ride-sharing service: Design | S |
| 299 | Web crawler: Design | S |
| 300 | Proximity service: Design | S |

---

# PART 12: Deep Dives (Topics 301–355)

*One concept = one video. Go deep on internals.*

## 12.1 Data & Storage Internals (301–311)

| # | Topic | Level |
|---|--------|--------|
| 301 | B-tree index: Why databases use it | I |
| 302 | Secondary indexes: Cost and use | I |
| 303 | Write-ahead log (WAL): What and why | I |
| 304 | Database connection pooling in practice | I |
| 305 | Time-series DB: Why different (Gorilla compression) | I |
| 306 | Inverted index: How search engines work | I |
| 307 | LSM Tree: How write-heavy databases work | I |
| 308 | Bloom filter: The fastest way to say "probably yes" | I |
| 309 | Geospatial indexing: Finding nearby things fast | I |
| 310 | MVCC: Multi-version concurrency control | S |
| 311 | When to use NewSQL (Spanner, CockroachDB) | S |

## 12.2 Caching & CDN Internals (312–317)

| # | Topic | Level |
|---|--------|--------|
| 312 | Cache key design: What to include | I |
| 313 | Cache poisoning: How to prevent | I |
| 314 | Stale reads: When acceptable | I |
| 315 | Edge caching: Cache control headers | I |
| 316 | Redis persistence: RDB vs AOF | I |
| 317 | Redis cluster: Slots and routing | I |

## 12.3 Reliability, Ops & Observability (318–329)

| # | Topic | Level |
|---|--------|--------|
| 318 | Blue-green vs canary deployment | I |
| 319 | Feature flags: Safe rollout | I |
| 320 | Runbooks and incident response | I |
| 321 | Post-mortems: Blameless incident review | I |
| 322 | Observability: Logs, metrics, traces | I |
| 323 | Structured logging: Logs that machines can read | I |
| 324 | Alerting strategy: What to alert on (and what not to) | I |
| 325 | SLO, SLI, error budget (basics) | I |
| 326 | Capacity planning: How to do it | I |
| 327 | Cost modeling: Major drivers | I |
| 328 | Rollback: When and how | I |
| 329 | Migration without downtime: Strategies | S |

## 12.4 Security & Compliance (330–334)

| # | Topic | Level |
|---|--------|--------|
| 330 | Principle of least privilege | I |
| 331 | Secrets management: Don't hardcode | I |
| 332 | Encryption at rest vs in transit | I |
| 333 | Audit logging: What and why | I |
| 334 | Data deletion and right to erasure | I |

## 12.5 Kafka & Event Systems (335–341)

| # | Topic | Level |
|---|--------|--------|
| 335 | Kafka: Topic, partition, offset | I |
| 336 | Kafka consumer groups | I |
| 337 | Kafka: Ordering and partitioning key | I |
| 338 | Kafka retention and compaction | I |
| 339 | Kafka exactly-once: Limits | I |
| 340 | When Kafka is overkill | I |
| 341 | Pub/Sub vs Kafka: When which? | I |

## 12.6 Data Pipelines (342)

| # | Topic | Level |
|---|--------|--------|
| 342 | Data pipelines: ETL and ELT basics | I |

## 12.7 Staff-Level Nuances (343–355)

| # | Topic | Level |
|---|--------|--------|
| 343 | Replication lag: Monitoring and handling | S |
| 344 | Cross-region consistency: The real trade-offs | S |
| 345 | When "eventually consistent" is not OK | S |
| 346 | Designing for partial failure (checklist) | S |
| 347 | Organizational scaling: APIs and ownership | S |
| 348 | Platform vs product team mindset | S |
| 349 | Deprecation and migration of APIs | S |
| 350 | What interviewers probe at Staff level | S |
| 351 | Rejecting the wrong solution (out loud) | S |
| 352 | Scope creep in interviews: How to avoid | S |
| 353 | Drawing and explaining in 2 minutes | S |
| 354 | End of interview: What to say | S |
| 355 | Learning path: From beginner to Staff (roadmap) | S |

---

# PART 13: Bonus Topics (Topics 356–368)

| # | Topic | Level |
|---|--------|--------|
| 356 | OpenID Connect vs OAuth (identity layer) | I |
| 357 | Idempotency keys: Client-generated | I |
| 358 | Deduplication windows in event systems | I |
| 359 | Redundant requests: When to dedupe | I |
| 360 | Feature flags and experimentation (A/B) | I |
| 361 | Data lineage and governance (intro) | I |
| 362 | Cold start: Serverless and caches | I |
| 363 | Warm pool and pre-warming | I |
| 364 | Three-phase commit (3PC): Why rarely used | S |
| 365 | Multi-region failover: Decision process | S |
| 366 | Hybrid Logical Clocks (HLC) (idea) | S |
| 367 | CRDT: Conflict-free replicated data types (intro) | S |
| 368 | Event sourcing vs event-driven (difference) | S |

---

## Summary

| Part | Range | Count | Level Focus |
|------|-------|-------|-------------|
| Part 1: Absolute Basics | 001–030 | 30 | B |
| Part 2: System Design Framework | 031–043 | 13 | B → I |
| Part 3: Networking | 044–064 | 21 | B → I → S |
| Part 4: Data & Databases | 065–107 | 43 | B → I → S |
| Part 5: Caching | 108–125 | 18 | B → I → S |
| Part 6: APIs & Security | 126–149 | 24 | B → I → S |
| Part 7: Distributed Systems | 150–192 | 43 | I → S |
| Part 8: Failure & Reliability | 193–210 | 18 | I → S |
| Part 9: Architecture Patterns | 211–231 | 21 | B → I → S |
| Part 10: Staff Mindset & Interview | 232–252 | 21 | I → S |
| Part 11: Design Problems | 253–300 | 48 | I → S |
| Part 12: Deep Dives | 301–355 | 55 | I → S |
| Part 13: Bonus | 356–368 | 13 | I → S |

**Total: 368 topics · 368 scripts in `Video_Scripts/`**

---

## Complexity Progression

```
Topic 001                                                    Topic 368
  |                                                              |
  ▼                                                              ▼
  B ─────────────────► I ──────────────────────────► S ─────────►
  │                    │                              │
  │ What is a system?  │ Sharding, CAP, Caching      │ CRDTs, Paxos
  │ What is an API?    │ Kafka, Circuit Breakers      │ Staff interviews
  │ What is DNS?       │ URL Shortener design         │ Ride-sharing design
  │ Docker, CI/CD      │ Kubernetes, Auto-scaling     │ Technical debt, RFCs
  │                    │                              │
  └── Topics 001-030   └── Topics 031-274             └── Topics 275-368
      (Intern)              (Mid → Senior)                (Senior → Staff)
```

---

## How to Study

1. **Phase 1 — Build Foundation (Topics 001–125):** Basics, framework, networking, databases, caching. Watch all in order.
2. **Phase 2 — Go Deeper (Topics 126–231):** APIs, distributed systems, failure modes, architecture. This is where it gets real.
3. **Phase 3 — Level Up (Topics 232–368):** Staff mindset, design problems, deep dives, bonus. Interview-ready.

**Time estimate:** At 5 min/video, the full series is ~30 hours of content.
