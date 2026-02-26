# Design Problem: Unique ID Generator

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

Every tweet. Every order. Every user. They need an ID. Not just unique in one database—unique *everywhere*. Across services. Across data centers. No coordination. No single counter. And ideally, sortable. "Design a unique ID generator." It sounds simple. It's not. Let's break it down.

---

## The Story

**Requirements first.** Globally unique—no collisions across machines, services, or time. **Sortable**—IDs generated later should sort after IDs generated earlier. Useful for indexes, "recent first" queries. **High throughput**—millions of IDs per second. No single bottleneck. **No coordination**—each node generates IDs independently. No database round-trip. No central server. That's the challenge.

**Option 1: UUID.** 128 bits. Random (v4) or time-ordered (v7). Globally unique. No coordination. But v4 isn't sortable. v7 is—timestamp in first 48 bits. Trade-off: 128 bits is large. 36 characters as string. Index overhead. Fine for many systems. Stripe uses UUIDs. Works.

**Option 2: Database auto-increment.** Simple. Ordered. Unique. But it's a **single point of coordination**. Every ID = one DB round-trip. At 100K IDs/sec, the database becomes the bottleneck. And if you shard? Each shard has its own sequence. Collision. Database IDs work for single-DB systems. Not for distributed.

**Option 3: Snowflake.** 64 bits. Timestamp (41 bits) + machine ID (10 bits) + sequence (12 bits). Generated locally. No coordination. Sortable—timestamp is first. Throughput: 4096 IDs per millisecond per machine. Scale by adding machines. Twitter invented it. Discord, Instagram use it. The catch: **clock sync**. If clocks drift, IDs can go backwards. You need **NTP** and possibly **boundary handling** (refuse to generate if clock moved backwards).

---

## Another Way to See It

Think of license plates. One state: sequential. 1, 2, 3. Add another state: collision. Solution: state code + number. TX-12345, CA-12345. Snowflake is the same. Timestamp = "when." Machine ID = "where." Sequence = "which one this millisecond." Combine them. Globally unique. Sortable. No central authority.

---

## Connecting to Software

- **Requirements:** Globally unique, sortable, high throughput (millions/sec), no coordination.
- **UUID v4:** Random. 128 bits. No coordination. Not sortable. Index fragmentation.
- **UUID v7:** Time-ordered. 128 bits. Sortable. Newer. Good choice when 128 bits is acceptable.
- **Database sequence:** `nextval()`, `AUTO_INCREMENT`. Ordered. Bottleneck. Single point of failure.
- **Snowflake:** 64 bits = timestamp (41) + machine (10) + sequence (12). ~69 years of timestamps. 1024 machines. 4096 IDs/ms/machine. **Clock sync critical.** NTP. Reject if clock goes backwards.
- **Real tools:** **Twitter Snowflake**, **Sonyflake** (Japan), **ULID** (128-bit, time-ordered). **Kafka** uses similar for offsets.

---

## Let's Walk Through the Diagram

```
SNOWFLAKE ID STRUCTURE (64 bits)
================================

| 1 bit | 41 bits    | 10 bits   | 12 bits  |
| unused| timestamp  | machine  | sequence |
|       | (ms since  | ID       | (0-4095) |
|       |  epoch)    |          |          |

Example: 1234567890123456789
         ^^^^^^^^^^^ ^^^^ ^^^^
         timestamp   mach seq

Flow:
  Client → ID Service (per machine)
         → Get current ms
         → Increment sequence (reset each ms)
         → Combine: timestamp | machine | seq
         → Return 64-bit ID

No DB. No network. Pure local computation.
```

---

## Real-World Examples (2-3)

**1. Twitter (X):** Invented Snowflake. Every tweet gets a Snowflake ID. Sortable. No coordination. Handles millions of tweets per second. The 64-bit format is compact—efficient for storage and indexes.

**2. Discord:** Uses Snowflake-like IDs for messages, users, servers. Sortable by creation time. They've published their implementation. Similar structure. Different epoch. Same idea.

**3. Stripe:** Uses UUIDs for object IDs (`cus_xxx`, `ch_xxx`). They don't need sortability from the ID—they use `created` timestamps. UUIDs give them global uniqueness without coordination. Different trade-off. Same goal.

---

## Let's Think Together

**Question:** You're building an order system. 50,000 orders per second. IDs must be unique. You'll frequently query "recent orders" (last hour). Snowflake or UUID v7?

**Pause. Think about it...**

**Answer:** **Snowflake** is the stronger choice. 64 bits vs 128—smaller indexes, less storage. Sortable—"recent orders" can use ID range (ID > X) efficiently. 50K/sec is well within Snowflake's capacity (4096/ms/machine = 4M/sec per machine). UUID v7 works too—sortable, unique. But Snowflake is more compact and designed for this scale. The trade-off: Snowflake needs machine ID assignment and clock discipline. UUID v7 is simpler—no coordination. If you want simplicity, v7. If you want density and scale, Snowflake.

---

## What Could Go Wrong? (Mini Disaster Story)

A company deployed Snowflake across 500 machines. Each had a unique machine ID. Worked for months. Then a **clock skew** event. One data center's NTP failed. Clocks drifted. Machines generated IDs with "past" timestamps. IDs went backwards. Sort order broke. Duplicate-ish IDs in some edge cases. Queries for "recent" returned wrong results. The fix: they added **clock sanity checks**. Before generating, compare system time to last generated timestamp. If clock moved backwards, wait or refuse. They also improved NTP monitoring. Lesson: Snowflake assumes monotonic clocks. If clocks lie, IDs lie. Protect against it.

---

## Surprising Truth / Fun Fact

Snowflake's 41-bit timestamp gives you ~69 years from epoch (custom epoch, e.g., 2010). Twitter won't run out until 2079. The 10-bit machine ID = 1024 machines. The 12-bit sequence = 4096 IDs per millisecond per machine. So one machine: 4 million IDs per second. Ten machines: 40 million. The design is elegant—every bit has a job. No waste.

---

## Quick Recap (5 bullets)

- **Requirements:** Globally unique, sortable, high throughput, no coordination.
- **UUID:** v4 random, v7 time-ordered. 128 bits. No coordination. v7 is sortable.
- **Database:** Simple but bottleneck. Single point. Breaks with sharding.
- **Snowflake:** 64 bits. Timestamp + machine + sequence. Sortable. No coordination. Clock sync critical.
- **Trade-off:** UUID = simpler. Snowflake = denser, higher scale. Choose based on throughput and storage needs.

---

## One-Liner to Remember

> **Snowflake: 64 bits, sortable, no coordination. Clock sync is critical. UUID v7: 128 bits, simpler. Database: simple but doesn't scale.**

---

## Next Video

IDs are solved. Next design problem: **typeahead autocomplete**. You type "netfl" and suggestions appear. Low latency. Ranked. Handles typos. How do you build it? Trie, caching, pre-computation. Real-world: Google search bar, YouTube. Let's design it.
