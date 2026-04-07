# Welcome to System Design: From Intern to Staff Engineer

## Video Length: ~6 minutes | Level: Everyone

---

## Hook (20-30 seconds)

Right now, somewhere in the world, someone taps "Buy" on Amazon. One tap. One second. Behind that tap? 100+ services wake up. Inventory checks stock. Payments charges a card. Warehouse picks a box. Shipping finds a route. Notifications send confirmations. Recommendations update. Fraud detection watches. All of that—in under a second. You didn't see any of it. But someone had to *design* all of it. That someone? That's what this series will turn you into. Welcome to System Design—from absolute zero to Staff-level engineer. 368 videos. Let's go.

---

## Why This Series Exists

Let me be honest with you.

When I first heard "system design," I was lost. Completely lost. People threw around words like "sharding," "consistent hashing," "CAP theorem," "event-driven architecture." I nodded. I smiled. I understood nothing.

I looked for resources. Textbooks? 800 pages of theory. Blog posts? Assumed I already knew everything. YouTube videos? Either too shallow—"here's a 2-minute overview"—or too deep—"let's implement Raft consensus from scratch." Nothing started at zero. Nothing took me by the hand and walked me through it, step by step, one concept at a time.

So I built what I wished existed. This series. 368 videos. Starting from "What is a system?" and ending at Staff-level design problems. No prerequisites. No assumptions. Just you, me, and one idea per video.

---

## How This Series Works

**One topic per video.** Not three. Not five. One. Each video is 4–5 minutes. Short enough to watch on a lunch break. Dense enough to actually teach you something.

**Three levels.** Every topic is tagged:

- **B (Beginner)** — You're new. Intern-level. Zero assumptions.
- **I (Intermediate)** — You've been working 1–2 years. You're building things. You need depth.
- **S (Senior / Staff)** — You're designing systems. Leading projects. Making architectural decisions.

**Watch in order.** Topic 001 assumes you know nothing. Topic 368 assumes you know everything before it. The series is a staircase. Each step builds on the last.

**Every video follows the same structure:**

1. A hook — a story or question to grab your attention
2. An analogy — something from everyday life that makes the concept click
3. A diagram — so you can see the idea, not just hear it
4. Real-world examples — Netflix, Amazon, Uber, WhatsApp—how the concept lives in the wild
5. A disaster story — what goes wrong when you get it wrong
6. A recap — so the idea sticks

---

## The Full Roadmap — 13 Parts, 368 Topics

Think of this as climbing a mountain. Here's the full map before we start.

---

### PART 1: Absolute Basics (Topics 001–030) · Beginner

Zero assumptions. We start with: What is a system? What is a server? What happens when you type a URL? What is a database? What is caching? What is Docker? If you've never written a line of code, you'll follow along. If you know the basics—watch anyway. You might be surprised.

> **30 topics · Topics: 001–030**

---

### PART 2: System Design Framework (Topics 031–043) · Beginner → Intermediate

Before we design anything, we learn *how* to think about design. Requirements. Capacity estimation. Back-of-the-envelope math. Vertical vs horizontal scaling. When to split a monolith. This is the mental framework you'll use in every interview and every real system.

> **13 topics · Topics: 031–043**

---

### PART 3: Networking Fundamentals (Topics 044–064) · Beginner → Senior

HTTP, HTTPS, REST, DNS, TLS, reverse proxies, load balancers, CDNs, TCP vs UDP, the OSI model. Everything that happens *between* your user and your server. You can't design systems without understanding how data moves.

> **21 topics · Topics: 044–064**

---

### PART 4: Data & Databases (Topics 065–107) · Beginner → Senior

SQL, NoSQL, indexes, transactions, ACID, sharding, consistent hashing, read replicas, schema migrations, object storage, backups. Databases are at the center of almost every system. This part goes deep.

> **43 topics · Topics: 065–107**

---

### PART 5: Caching (Topics 108–125) · Beginner → Senior

Why cache? Where to cache? Cache-aside, write-through, write-back. Cache invalidation—the hardest problem in computer science. Eviction policies. Cache stampede. Redis internals. CDN caching. One wrong cache decision can take down your whole system.

> **18 topics · Topics: 108–125**

---

### PART 6: APIs, Communication & Security (Topics 126–149) · Beginner → Senior

REST, gRPC, GraphQL, WebSockets, webhooks. API versioning, idempotency, request tracing. Authentication, authorization, JWT, OAuth, RBAC. Rate limiting algorithms. This part covers everything that crosses the boundary between your services and the outside world.

> **24 topics · Topics: 126–149**

---

### PART 7: Distributed Systems (Topics 150–192) · Intermediate → Staff

This is where it gets real. Consistency, CAP theorem, quorum, BASE. Raft, Paxos, leader election, gossip protocols, vector clocks. Distributed transactions, SAGA, outbox pattern, delivery semantics. Circuit breakers, retries, dead letter queues. The concepts that separate engineers who build features from engineers who design systems.

> **43 topics · Topics: 150–192**

---

### PART 8: Failure, Reliability & Availability (Topics 193–210) · Intermediate → Staff

Everything fails. The question is: how gracefully? Partial failure, cascading failure, timeouts, health checks, load shedding, bulkheads, chaos engineering. Multi-region, active-active, RPO and RTO, disaster recovery. This part teaches you to design for the worst day, not the best day.

> **18 topics · Topics: 193–210**

---

### PART 9: Architecture Patterns & Styles (Topics 211–231) · Beginner → Staff

Monolith vs microservices. Strangler fig. Event-driven architecture. Serverless. API gateways. Service discovery. Service mesh. WebSockets, SSE, long polling. Kubernetes, auto-scaling, CQRS, event sourcing. The patterns that underpin how modern systems are built.

> **21 topics · Topics: 211–231**

---

### PART 10: Staff-Level Mindset & Interview (Topics 232–252) · Intermediate → Staff

What separates L5 from L6 isn't knowledge—it's judgment. Scope, impact, ownership, trade-offs, ambiguity. Writing RFCs. Managing technical debt. And then: how to demonstrate all of that in a 45-minute system design interview. Driving the conversation. Defending your design. Knowing when to go deep vs stay high-level.

> **21 topics · Topics: 232–252**

---

### PART 11: Design Problems (Topics 253–300) · Intermediate → Staff

Real designs, broken down step by step. URL shortener. Rate limiter. Notification system. Chat system. Payment platform. News feed. Ride-sharing. Video streaming. Web crawler. Each design covers: problem, requirements, high-level architecture, key components, scaling decisions, failure modes, and trade-offs.

> **48 topics · Topics: 253–300**

---

### PART 12: Deep Dives (Topics 301–355) · Intermediate → Staff

One concept = one video. B-trees, LSM trees, bloom filters, inverted indexes, MVCC. Redis internals, Kafka internals, CDC. Observability, SLOs, canary deploys, chaos engineering. Security, compliance, secrets management. This is where you become dangerous.

> **55 topics · Topics: 301–355**

---

### PART 13: Bonus Topics (Topics 356–368) · Intermediate → Staff

The concepts that don't fit neatly anywhere else but matter at scale: CRDTs, hybrid logical clocks, 3-phase commit, multi-region failover, event sourcing vs event-driven, idempotency keys, data lineage. Staff-level finishing touches.

> **13 topics · Topics: 356–368**

---

## The Map at a Glance

| Part | Topics | Count | Level |
|------|--------|-------|-------|
| 1: Absolute Basics | 001–030 | 30 | Beginner |
| 2: System Design Framework | 031–043 | 13 | Beginner → Intermediate |
| 3: Networking | 044–064 | 21 | Beginner → Senior |
| 4: Data & Databases | 065–107 | 43 | Beginner → Senior |
| 5: Caching | 108–125 | 18 | Beginner → Senior |
| 6: APIs & Security | 126–149 | 24 | Beginner → Senior |
| 7: Distributed Systems | 150–192 | 43 | Intermediate → Staff |
| 8: Failure & Reliability | 193–210 | 18 | Intermediate → Staff |
| 9: Architecture Patterns | 211–231 | 21 | Beginner → Staff |
| 10: Staff Mindset & Interview | 232–252 | 21 | Intermediate → Staff |
| 11: Design Problems | 253–300 | 48 | Intermediate → Staff |
| 12: Deep Dives | 301–355 | 55 | Intermediate → Staff |
| 13: Bonus Topics | 356–368 | 13 | Intermediate → Staff |
| **Total** | **001–368** | **368** | **Intern → Staff** |

---

## Three Study Phases

You don't have to watch all 368 videos before you're useful. Here's how to think about it in phases:

**Phase 1 — Build the Foundation (Topics 001–125)**
Absolute basics, system design framework, networking, databases, caching. ~10 hours. After this, you'll understand how software actually works. You'll stop nodding and start understanding.

**Phase 2 — Go Deeper (Topics 126–231)**
APIs, distributed systems, failure modes, architecture patterns. ~9 hours. After this, you can hold your own in any engineering design conversation.

**Phase 3 — Level Up (Topics 232–368)**
Staff mindset, design problems, deep dives, bonus topics. ~11 hours. After this, you're interview-ready and staff-level thinking is second nature.

---

## Who Is This For?

**Students** who want to understand how real software works—not just how to write a for-loop.

**New engineers** who just started their first job and hear words they don't understand in meetings.

**Mid-level engineers** who can build features but freeze when asked "how would you design this at scale?"

**Senior engineers** preparing for Staff-level interviews where the bar is architecture, trade-offs, and leadership.

**Anyone curious** about how Instagram delivers your feed, how Uber finds you a driver, or how your bank keeps your money safe.

If you've ever wondered "how does this actually work?"—this series is for you.

---

## What Makes This Different?

Three things.

**First: it starts at zero.** Most system design content assumes you already know what a load balancer is. I don't. Topic 001 is "What is a system?" We earn every concept.

**Second: it's one staircase.** 368 topics in strict order of increasing complexity. No jumping around. No "watch this playlist for beginners, that playlist for advanced." One path. One climb. Intern to Staff.

**Third: every concept gets a story.** Not just definitions. Stories. Analogies. Disasters. I want you to *feel* why consistency matters—not just memorize that CAP theorem has three letters. When you remember the restaurant that lost your order because the waiter and the chef stopped talking, you'll never forget what a broken connection means in a distributed system.

---

## The Numbers

- **368 topics** — from "What is a server?" to "Design a ride-sharing platform"
- **~5 minutes per video** — respect for your time
- **~30 hours total** — less than one season of a TV show, and it could change your career
- **13 parts** — organized from basics to Staff-level mastery
- **3 levels** — Beginner, Intermediate, Senior/Staff — every topic tagged

---

## Quick Recap

- 368 videos, intern to Staff. 13 parts. One topic per video.
- Each video: ~5 min, with analogy, diagram, real-world examples, disaster story, recap.
- Watch in order. Topic 001 = zero knowledge. Topic 368 = Staff-level.
- Three study phases: Foundation (001–125), Deeper (126–231), Level Up (232–368).
- No prerequisites. Just curiosity.

---

## One-Liner to Remember

> **368 steps from "What is a server?" to "Design a ride-sharing platform." One video at a time. Let's climb.**

---

## Next Video

Enough talking about the journey. Let's start it. Topic 001: **What is a "system" in software?** I'll explain it using a restaurant. No code. No jargon. Just a kitchen, a waiter, and a hungry customer. See you there.
