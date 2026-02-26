# Cache vs Read Replica: When to Use Which?

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

You're at a busy library. Two ways to get a popular book faster: photocopy the first chapter and keep it at your desk—instant, but maybe outdated. Or ask the librarian to order a second copy and put it on a different shelf—still the real book, just in another location. The photocopy is your cache. The second shelf copy is your read replica. Both speed things up. But they're not the same. When do you use which?

---

## The Story

Your database is drowning in read traffic. Writes go to the primary. Reads? Same primary. Every product page view, every profile load, every search—all hitting one machine. The primary is a bottleneck. You need relief.

Two options appear. **Option A: Read replica.** A copy of your database that stays in sync with the primary. Writes go to primary. Replication streams changes. Reads can go to the replica. Same data. Same schema. Just another copy. **Option B: Cache.** In-memory store like Redis. You put frequently-accessed data there. Sub-millisecond reads. But the cache doesn't have everything. It has what you've put in it. And it can be stale.

Here's the key: a **read replica** is still a full database. It has the complete dataset. It's eventually consistent—a few milliseconds or seconds behind the primary. But it's the real data. A **cache** is a subset. Hot data only. Blazing fast. But if it's not there, you miss. And if the source changed, the cache might not know yet.

**Latency vs freshness.** Cache wins on latency—Redis is 0.5ms. A read replica might be 5–20ms. But a replica is fresher—it's replicating continuously. Cache? You decide the TTL. Or you invalidate on write. Cache gives you control over staleness. Replica gives you "almost real-time" by default.

**When to use which?** High read volume, need full dataset access, can tolerate slight lag? Read replica. Need sub-millisecond response, data is read-heavy and changes slowly? Cache. Often you use both. Cache for the hottest 1% of data. Read replica for the rest. Redis plus MySQL read replica. Best of both worlds.

---

## Another Way to See It

A restaurant with two locations. **Read replica:** The second location has a full copy of the menu. Same dishes. Maybe the chef just added a special—it'll show up in a few minutes. **Cache:** The host keeps a sticky note with "today's top 3 sellers" at the front. Instant. But if the kitchen runs out of the top seller, the sticky note might be wrong until someone updates it. Replica = full menu, slightly delayed. Cache = hot subset, you control freshness.

---

## Connecting to Software

- **Read replica** = physical copy of the database. **Replication** streams writes from primary to replica(s). MySQL, PostgreSQL, MongoDB—all support this. You route read queries to replicas. Write queries stay on primary. **Replication lag** is the delay. Usually milliseconds to seconds.
- **Cache** = in-memory key-value store. **Redis**, **Memcached**. You explicitly put data in. Cache-aside: app checks cache, misses go to DB, result cached. Cache doesn't know the schema. It stores blobs. You decide what to cache and when to invalidate.
- **Combine both:** Primary for writes. Read replica for "full dataset" reads that don't need primary. Cache for the hottest keys—user sessions, trending products, leaderboards. Redis + MySQL read replica is the standard stack at scale.
- **Latency:** Cache ~0.5ms. Read replica ~5–50ms (network + disk). Cache wins. **Freshness:** Replica is continuously synced. Cache is as fresh as your TTL or invalidation logic.
- **Cost:** Replicas need similar hardware to primary. Cache is RAM. Often cheaper per read at extreme scale.

---

## Let's Walk Through the Diagram

```
    WRITE REQUEST                    READ REQUEST
         │                                │
         ▼                                ▼
    ┌─────────┐                    ┌─────────────┐
    │ Primary │                    │ Check Cache │
    │   DB    │                    └──────┬──────┘
    └────┬────┘                           │
         │ Replication              ┌─────┴─────┐
         ▼                          │           │
    ┌─────────┐                   HIT         MISS
    │ Read    │                     │           │
    │ Replica │                     ▼           ▼
    └────┬────┘                Return      Query Read
         │                     cached      Replica
         │                     (0.5ms)    (5-20ms)
         │                          │           │
         └──────────────────────────┴───────────┘
                    Both serve reads
```

**Flow:** Writes hit primary only. Reads: check cache first. Hit? Return. Miss? Query read replica. Optionally cache the result for next time. Primary stays free for writes. Replica handles "full data" reads. Cache handles "hot data" reads. Three-tier read path.

---

## Real-World Examples (2-3)

**Example 1 — E-commerce product catalog:** Product details change occasionally. Cache product pages in Redis—sub-millisecond for millions of views. Product search, filters, "products by category"? Read replica. Full dataset. Slightly slower but complete. Cache + replica. Both.

**Example 2 — Social media feed:** Your feed is computed. Stored somewhere. The computed feed for "user 123" is cached—Redis. When you scroll, cache hit. When you need "all posts from user X" for a profile page? Read replica. Full query. Cache for hot. Replica for full.

**Example 3 — Uber ride status:** Your active ride status? Cached. Blazing fast. Driver location updates? Written to primary. Read by you from replica—or cached if it's your ride. Real-time enough. Cache for "my ride right now." Replica for "ride history" or admin queries.

---

## Let's Think Together

You have 10 million products. 1000 are "trending" and get 80% of views. Do you cache all 10 million? Or just the 1000?

Pause and think.

Cache the 1000. Or 10,000. The hot subset. Cache has limited memory. Use it for what matters. The other 9.99 million? Read replica. When someone requests a long-tail product, replica serves it. Slower but fine. Cache for the head. Replica for the tail. Combine both. Don't try to cache everything.

---

## What Could Go Wrong? (Mini Disaster Story)

A fintech startup used only a read replica for reads. No cache. Replication lag spiked to 30 seconds during a flash sale. Users saw stale balances. "I just got paid but my balance shows zero!" Panic. Support flooded. They added Redis cache for account balances—with aggressive invalidation on every write. Cache for speed. Invalidation for correctness. Replica for everything else. Lesson: replication lag can bite. For critical "must be fresh" data, cache with invalidation beats replica alone. Know your freshness requirements.

---

## Surprising Truth / Fun Fact

Instagram runs one of the largest Redis deployments in the world—hundreds of gigabytes of cached data. But they also use read replicas for MySQL. Cache for feed, stories, counts. Replica for full queries, analytics, admin tools. Neither alone. Both together. That's how you scale reads.

---

## Quick Recap (5 bullets)

- **Read replica** = full copy of DB, replication lag, good for "full dataset" reads.
- **Cache** = in-memory subset, you control what's cached, sub-millisecond latency.
- **Latency:** Cache wins (0.5ms vs 5–50ms). **Freshness:** Replica is continuously synced; cache needs TTL or invalidation.
- **Combine both:** Cache for hottest data. Replica for the rest. Redis + MySQL replica = standard stack.
- **Trade-off:** Replica = complete but slower. Cache = fast but partial. Use the right tool for each read pattern.

---

## One-Liner to Remember

> Read replica = full database copy for reads. Cache = in-memory hot subset. Use both: cache the head, replica the tail.

---

## Next Video

You're sending data between services. JSON? Protobuf? Avro? The format you choose affects size, speed, and compatibility. Next: **Data Serialization—JSON vs Protobuf vs Avro.** Why format matters. See you there.
