# How to Generate Unique IDs at Scale

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

A bakery. One person hands out numbers. "You're 47. You're 48. You're 49." Simple. Sequential. Unique. Now imagine 100 bakeries. Each hands out numbers independently. Bakery A gives 47. Bakery B gives 47. Collision. Same number, different places. At scale, "just increment" breaks. You need IDs that are unique *everywhere*—no coordination, no single counter. That's the challenge of unique ID generation.

---

## The Story

In a single database, **auto-increment** works. Row 1 gets ID 1. Row 2 gets ID 2. The database guarantees uniqueness. Simple. But scale breaks it. Two databases? Both start at 1. You merge data—duplicate IDs. **Sharding?** Each shard has its own sequence. Shard A: 1, 2, 3. Shard B: 1, 2, 3. Collision. **Multiple services** creating IDs? No single source of truth. You need IDs that are globally unique without a central coordinator.

**UUID v4**—random 128 bits. Probability of collision is astronomically low. Generate anywhere. No coordination. But here's the catch: **random IDs don't sort.** Insert ID `a3f2...` then `b1c4...` then `a2e9...`. In a **B-tree index**, inserts go all over the place. Page splits. Fragmentation. Slow. **UUID v7** fixes that—time-ordered. First 48 bits are a timestamp. IDs generated in the same millisecond sort together. Better for indexes. Better for "show me recent records."

**Database sequences** work when you have one database. `SELECT nextval('user_id_seq')`. Unique. Ordered. But it's a bottleneck. Every ID requires a round-trip. At 100,000 inserts per second, the sequence becomes the choke point. **Snowflake**-style IDs solve that: timestamp + machine ID + sequence. Generated locally. No coordination. Sortable. We'll cover Snowflake in the next video.

---

## Another Way to See It

License plates. Sequential in one state: 1, 2, 3. But across states? Texas has plate 12345. California has 12345. Add a state code: TX-12345, CA-12345. Now unique. The "state code" is like a machine ID. The number is like a sequence. Combine them. Globally unique.

---

## Connecting to Software

- **Auto-increment:** Works in single DB. Breaks with sharding, multiple DBs, distributed systems. Single point of coordination.
- **UUID v4:** 128 bits, random. Globally unique. No coordination. **Trade-off:** Random = poor index performance. Inserts scattered. Page splits.
- **UUID v7:** Time-ordered. First 48 bits = timestamp. Sortable. Better for **B-tree indexes**. Newer standard (2022). Libraries adding support.
- **Database sequences:** `nextval()` in PostgreSQL, `AUTO_INCREMENT` in MySQL. Ordered. Unique. Bottleneck at high throughput.
- **Why ordering matters:** B-tree indexes append new rows at the "end" when IDs are sequential. Random IDs cause random inserts = fragmentation = slower writes and range scans.
- **Real tools:** PostgreSQL `gen_random_uuid()` (v4), `uuid_generate_v7()` (extension). MySQL `UUID()`. Application-level: **ulid**, **nanoid** (different formats, similar ideas).

---

## Let's Walk Through the Diagram

```
AUTO-INCREMENT (single DB)          UUID v4 (distributed)
========================           =====================

DB: id = 1, 2, 3, 4, 5...          Service A: a3f2b1c4-...
                                   Service B: 7e9d2a1b-...
One source. Ordered.               Service C: 1b4c8e3f-...
                                   No coordination. Random order.

UUID v7 (time-ordered)             SNOWFLAKE (next video)
======================            =======================
018e7b2f-a1b2-...  (earlier)      64 bits: timestamp + machine + seq
018e7b30-c3d4-...  (later)        Sortable. No coordination.
Timestamp in first 48 bits.        Used by Twitter, Discord.
Sortable. Index-friendly.
```

---

## Real-World Examples (2-3)

**1. Stripe:** Uses UUIDs for object IDs (`cus_xxx`, `ch_xxx`). Globally unique. No coordination between services. Can generate IDs in API servers, webhooks, workers—all independent.

**2. PostgreSQL at scale:** Auto-increment works until you shard. Then each shard needs its own range or a different strategy. Many teams switch to UUID v4 or v7 for user-facing IDs. Internal IDs might stay auto-increment; external IDs use UUIDs.

**3. Event sourcing:** Every event needs a unique ID. Events come from many producers. UUID v4 or v7. No central coordinator. Events can be generated anywhere, merged later. Ordering with v7 helps replay in time order.

---

## Let's Think Together

**Question:** You're building a social app. 10 million new posts per day. IDs must be unique. You'll query "recent posts" (last 24 hours) often. UUID v4 or UUID v7?

**Pause. Think about it...**

**Answer:** **UUID v7.** "Recent posts" means range queries by time. v7 embeds timestamp—IDs sort by creation time. Index on ID ≈ index on time. Efficient. v4 is random—you'd need a separate `created_at` column and index. v7 gives you both: uniqueness and implicit ordering. Same storage. Better queries.

---

## What Could Go Wrong? (Mini Disaster Story)

A company used auto-increment for order IDs. One database. Worked for years. Then they needed to merge with an acquired company. Both had order ID 1, 2, 3... Collision. They had to remap millions of IDs. Foreign keys broke. Reports broke. Migration took months. The fix? They should have used UUIDs from day one. Or at least composite IDs (tenant_id + sequence). Lesson: if there's any chance of multiple systems, mergers, or sharding, don't rely on single-DB auto-increment. Plan for distributed uniqueness early.

---

## Surprising Truth / Fun Fact

**UUID v7** was standardized in 2022 (RFC draft). Before that, many systems used **ULID**—Universally Unique Lexicographically Sortable Identifier—which has the same idea: timestamp + randomness. ULID is 128 bits, Crockford base32. v7 is the "official" UUID version with that property. The industry had been doing this informally for years.

---

## Quick Recap (5 bullets)

- **Auto-increment:** Simple. Works in single DB. Breaks with sharding, multiple DBs.
- **UUID v4:** Random. Globally unique. No coordination. **Trade-off:** Poor index performance (random inserts).
- **UUID v7:** Time-ordered. Sortable. Better for indexes and range queries. Newer standard.
- **Ordering matters:** Sequential IDs = efficient B-tree inserts. Random IDs = fragmentation.
- **Database sequences:** Ordered, unique. Bottleneck at high throughput. Consider distributed generation.

---

## One-Liner to Remember

> **At scale, auto-increment breaks. UUID v4 is unique but random. UUID v7 is unique and sortable. Choose based on whether you need ordering.**

---

## Next Video

UUID v7 is great. But what if you need something smaller? 64 bits instead of 128? **Snowflake IDs**—Twitter's solution. Timestamp + machine ID + sequence. Sortable. No coordination. Used by Twitter, Discord, Instagram. How it works. What could go wrong. Next: Snowflake IDs.
