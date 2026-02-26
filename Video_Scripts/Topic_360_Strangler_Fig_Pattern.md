# Strangler Fig Pattern: Migrating Away from a Monolith

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

You have a legacy monolith. Millions of lines. A decade of tech debt. You want microservices. The obvious path: rewrite everything. Big-bang. Two years. High risk. One bug and the whole thing fails. There's a smarter way: the **strangler fig pattern**. Named after a tree that grows around a host tree, eventually replacing it. You don't replace the monolith in one shot. You route new features to new services. You gradually move old features. The monolith stays running. One day, it's gone. Let me show you how.

---

## The Story

The **strangler fig pattern** is a migration strategy. You don't replace the monolith. You replace it *piece by piece*. The monolith keeps running. New functionality goes to new services. Old functionality gets gradually extracted. A **facade** or **proxy** layer sits in front. It routes traffic: new requests go to the new service. Old requests still go to the monolith. Over time, more traffic goes to new services. Less to the monolith. Eventually, the monolith is gone. Strangled.

**How it works:** Step 1: Add a routing layer. API gateway, reverse proxy, or a thin facade. Step 2: Route new features to new services. "Create order" goes to new order-service. "Get user" still goes to monolith. Step 3: Identify a bounded context from the monolith. Extract it. Build a new service. Route that traffic to the new service. Step 4: Repeat. User service. Payment service. Inventory service. Eventually, the monolith is empty. Decommission it.

**When to use:** Legacy system modernization. Monolith to microservices. You can't afford a big-bang rewrite. You need incremental migration. Business keeps running. No "stop the world" deployment. **Martin Fowler** documented this pattern. It's widely used in industry.

---

## Another Way to See It

Think of renovating a house while living in it. You don't tear it down and rebuild. You renovate room by room. Kitchen first. You still use the bathroom, bedroom. Then the bathroom. Then the bedroom. The house stays functional. One day, it's fully renovated. The strangler fig pattern is that for software. The monolith is the old house. New services are the renovated rooms. The routing layer is the door—it directs you to the right room.

---

## Connecting to Software

- **Strangler fig pattern** = incremental migration. Replace monolith piece by piece. Don't big-bang.
- **Facade / proxy** = routing layer. Routes traffic to new services or monolith.
- **Extract** = identify bounded context, build new service, route traffic.
- **When:** Legacy modernization. Monolith → microservices. Avoid risky rewrites.
- **Benefits:** Incremental. Risk spread over time. Business continuity. No downtime.
- **Real tools:** **API Gateway** (Kong, AWS API Gateway), **reverse proxy** (Nginx, Envoy), **service mesh** for routing.

---

## Let's Walk Through the Diagram

```
    PHASE 1: Add routing layer              PHASE 2: Extract services

    Clients ──► [Facade/Proxy] ──► Monolith    Clients ──► [Facade/Proxy]
                     │                                    │
                     │                                    ├──► Order Service (new)
                     │                                    ├──► User Service (new)
                     │                                    └──► Monolith (shrinking)
                     │
                     └── All traffic to monolith

    PHASE 3: Monolith gone

    Clients ──► [Facade/Proxy] ──► Order Service
                     │            User Service
                     │            Payment Service
                     └──────────── Inventory Service
```

---

## Real-World Examples (2-3)

**1. Amazon.** In the early 2000s, Amazon had a massive monolith. They couldn't scale it. They didn't rewrite. They extracted services one by one. Order service. User service. Payment. They routed traffic. The monolith shrank. Eventually, they had hundreds of services. The strangler fig pattern. Bezos's famous "API mandate" (teams must expose via API) supported this. Services replaced monolith pieces.

**2. Shopify.** Migrated from a Ruby monolith to a more modular architecture. They didn't rewrite. They extracted services. Strangler fig. New features in new services. Old features gradually extracted. They still have a monolith core, but it's smaller. Migration continues.

**3. Martin Fowler.** Documented the pattern in 2004. Named it after the strangler fig tree. The pattern became a standard playbook for legacy modernization. Many companies use it without knowing the name—they just "extract services incrementally."

---

## Let's Think Together

**Question:** You're extracting the "payment" module from the monolith. The monolith still has a database. The new payment service needs payment data. Do you share the database?

**Pause. Think about it...**

**Answer:** Sharing the database creates coupling. The monolith and new service both touch the same tables. Schema changes become a coordination nightmare. Better: **extract the data too**. The new payment service gets its own database. Migrate data. Or use **dual-write** during transition: monolith writes to both. New service reads from its DB. Eventually, monolith stops writing. Full migration. Database-per-service is the goal. The strangler fig applies to data too. Don't leave shared DB as permanent.

---

## What Could Go Wrong? (Mini Disaster Story)

A team started a strangler fig migration. They built a facade. They extracted the "notification" module. They routed traffic. But they forgot: the monolith called notification *internally*—from other modules. Those internal calls still went to the old code. The new service had the new logic. Inconsistency. Some notifications used the new flow. Some used the old. Bugs. Duplicate sends. The fix? Map *all* call paths. Internal and external. The facade must route both. Or refactor internal calls to use the new service as an API. Lesson: migration isn't just about the API. It's about every call path. Audit thoroughly.

---

## Surprising Truth / Fun Fact

The **strangler fig tree** grows from a seed in the canopy of a host tree. Its roots grow down, around the host. Eventually, they reach the ground. The fig grows. The host tree dies. The fig stands alone—a new tree, shaped by the old. The software pattern mimics that: the new services grow around the monolith. The monolith "dies" (decommissioned). The new architecture stands. Nature-inspired design.

---

## Quick Recap (5 bullets)

- **Strangler fig pattern** = incremental migration. Replace monolith piece by piece. No big-bang.
- **Facade/proxy** = routing layer. Routes new traffic to new services. Old traffic to monolith.
- **Extract** = identify bounded context, build service, route traffic. Repeat.
- **When:** Legacy modernization. Monolith → microservices. Avoid risky rewrites.
- **Benefits:** Incremental. Risk spread. Business continuity. No downtime.

---

## One-Liner to Remember

> **The strangler fig pattern: grow new services around the monolith. Route traffic gradually. One day, the monolith is gone.**

---

## Next Video

Your microservices serve a mobile app and a web app. But they need different data shapes. Mobile needs less data, smaller payloads. Web needs more. Do you build one API for all? Or one per client? That's **Backend for Frontend (BFF)**. That's next.
