# Database Per Service: Should Each Microservice Own Its Data?

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

You have 10 microservices. They all share one database. Order service. User service. Payment service. Same tables. Same schema. One change—add a column—and you coordinate with every team. A bug in one service can corrupt data for others. Scaling? The database becomes the bottleneck. Everyone hits it. There's another way: **database per service**. Each service owns its data. Its own database. Its own schema. Services talk via APIs or events. No shared tables. Let me show you the trade-offs and when it matters.

---

## The Story

**Database per service** means each microservice has its own database. Order service → orders DB. User service → users DB. Payment service → payments DB. No service touches another's database. They communicate via **APIs** or **events**. Need user data in the order service? Call the User API. Don't query the users table. Data ownership is clear. The User service owns user data. Period.

**Why?** **Shared database** problems: tight coupling. Schema changes require coordination. One team's migration breaks another. Scaling: one DB for everyone = bottleneck. Technology lock-in: everyone uses the same DB. Can't pick the right tool (e.g., Elasticsearch for search, Postgres for transactions). **Database per service** fixes this: **independent deployment**—change your schema, deploy your service. No coordination. **Independent scaling**—scale the order service and its DB. Others unaffected. **Technology choice**—User service uses Postgres. Analytics service uses a data warehouse. Each picks what fits.

**Challenges:** **Distributed transactions**—an order spans Order service and Payment service. Two databases. No single ACID transaction. You need **SAGA** (compensating transactions) or eventual consistency. **Data consistency**—eventual, not immediate. **Querying across services**—"orders for user X" requires calling Order API with user context. Or **API composition**—aggregate in a BFF or gateway. Or **CQRS**—separate read models. More complexity. But the benefits often outweigh it at scale.

**When is shared DB OK?** Small team. Early stage. Few services. Coordination is cheap. Migrating later is possible (strangler fig for data). Don't over-engineer from day one. But plan for the split.

---

## Another Way to See It

Think of departments in a company. HR has its own files. Finance has its own. Sales has its own. They don't walk into each other's filing cabinets. They request data through formal channels—email, tickets. Database per service is that. Each department (service) owns its files (database). They collaborate through defined interfaces. No one rummages through another's data.

---

## Connecting to Software

- **Database per service** = each microservice has its own DB. No shared tables.
- **Communication:** APIs or events. No direct DB access across services.
- **Benefits:** Independent deployment, scaling, technology choice. Loose coupling.
- **Challenges:** Distributed transactions → need **SAGA**. Cross-service queries → **API composition** or **CQRS**.
- **When shared DB is OK:** Small team, early stage. Don't over-engineer. Plan for split later.
- **Real-world:** Amazon, Uber, Netflix. Core pattern for microservices.

---

## Let's Walk Through the Diagram

```
    SHARED DATABASE (problematic)          DATABASE PER SERVICE

    ┌─────────┐ ┌─────────┐ ┌─────────┐   ┌─────────┐     ┌─────────┐     ┌─────────┐
    │ Order   │ │ User    │ │ Payment │   │ Order   │     │ User    │     │ Payment │
    │ Service │ │ Service │ │ Service │   │ Service │     │ Service │     │ Service │
    └────┬────┘ └────┬────┘ └────┬────┘   └────┬────┘     └────┬────┘     └────┬────┘
         │           │           │             │               │               │
         └───────────┼───────────┘             │               │               │
                     │                         │               │               │
                     ▼                         ▼               ▼               ▼
              ┌─────────────┐            ┌──────────┐   ┌──────────┐   ┌──────────┐
              │  One DB    │            │ Orders   │   │ Users    │   │ Payments │
              │  Everyone  │            │ DB       │   │ DB       │   │ DB       │
              │  touches    │            └──────────┘   └──────────┘   └──────────┘
              └─────────────┘                  │               │               │
                    Coupling                   └───────┬───────┴───────┬───────┘
                    Bottleneck                         APIs / Events
```

---

## Real-World Examples (2-3)

**1. Amazon.** Each service owns its data. Order service has order data. Inventory has inventory. They communicate via APIs and events. No shared database. Scale: millions of requests. Independent teams. Database per service is foundational. They've been doing it since the early 2000s.

**2. Uber.** Hundreds of microservices. Each with its own data store. Rider service. Driver service. Trip service. Billing. They use event-driven architecture. Services publish events. Others consume. Data stays in service boundaries. Cross-service queries via APIs or read models (CQRS).

**3. Netflix.** Microservices with database per service. They use **Cassandra**, **DynamoDB**, and others—different stores for different needs. Recommendation service has its own data. Playback service has its own. Loose coupling. Independent scaling.

---

## Let's Think Together

**Question:** Order service needs to display "user name" on the order. User data is in User service's DB. How does Order service get it?

**Pause. Think about it...**

**Answer:** Option 1: **API call**—Order service calls User API: "get user name for ID 123." Option 2: **Data duplication**—Order service stores a copy of user name when the order is created. Denormalized. No runtime call. Option 3: **Event**—When order is created, User service (or an event) provides user name. Order service stores it. Trade-off: API call = fresh data, latency. Duplication = stale data possible, no extra call. Most systems use duplication for display data. "Eventual consistency" is OK for "order created by John." You don't need real-time name changes on old orders.

---

## What Could Go Wrong? (Mini Disaster Story)

A team adopted database per service. Order service. Payment service. They implemented a "place order" flow. Order service creates order. Calls Payment service to charge. Payment succeeds. Order service writes to its DB. Network blip. Write fails. Order service retries. Payment already charged. Duplicate order? Or payment without order? They needed a **SAGA**. Compensating transaction: if order write fails, refund the payment. They didn't implement it. Result: charged customers with no order. Support nightmare. The fix? SAGA pattern. Or use a transactional outbox. Or two-phase commit (heavy). Lesson: database per service means distributed transactions. Plan for failure. Compensate. Don't assume both writes succeed.

---

## Surprising Truth / Fun Fact

**Two-phase commit (2PC)** can do distributed transactions across DBs. But it's slow. It blocks. It doesn't scale. Most microservice architectures avoid it. **SAGA** (compensating transactions) is the preferred approach. Each service does its part. If something fails, run compensating actions. "Payment succeeded but order failed? Refund." Eventually consistent. More resilient. Amazon, Uber—they use SAGA, not 2PC. The industry moved away from distributed 2PC for good reason.

---

## Quick Recap (5 bullets)

- **Database per service** = each microservice owns its data. Its own DB. No shared tables.
- **Benefits:** Independent deployment, scaling, technology choice. Loose coupling.
- **Challenges:** Distributed transactions → **SAGA**. Cross-service queries → **API composition** or **CQRS**.
- **Communication:** APIs or events. Never direct DB access across services.
- **When shared DB is OK:** Small team, early stage. Plan for split. Don't over-engineer.

---

## One-Liner to Remember

> **Database per service: each microservice owns its data. Communicate via APIs or events. Embrace eventual consistency.**

---

## Next Video

Your services own their data. Your system is distributed. Now the Staff-level question: how do you propose a major design change to your team? You write an **RFC**—Request for Comments. A document that spells out the problem, options, and recommendation. That's next.
