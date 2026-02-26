# Snowflake IDs: Twitter's Solution to Unique IDs

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

Twitter. Millions of tweets per second. Each needs a unique ID. Auto-increment? One database, one counter—bottleneck. UUID? 128 bits, random—too big, doesn't sort. Twitter needed: 64 bits, sortable, no coordination. They built **Snowflake**. A 64-bit ID that encodes timestamp, machine, and sequence. Generate locally. Never collide. Sort by time. Simple. Elegant. Now used by Discord, Instagram, and countless systems.

---

## The Story

**Snowflake** is a 64-bit ID. Three parts. **Timestamp**—41 bits. Milliseconds since epoch. Gives you ~69 years of IDs. **Machine ID**—10 bits. 1024 machines. Each node has a unique ID. **Sequence**—12 bits. 4096 IDs per millisecond per machine. Put them together: `timestamp | machine_id | sequence`. Generate on each machine. No coordination. No central server. As long as clocks are roughly synchronized and machine IDs are unique, you get globally unique, **sortable** IDs.

Why 64 bits? Fits in a `long` (int64). Half the size of UUID. Database-friendly. Index-friendly. Sortable by time—new tweets have bigger IDs. "Show recent tweets" = `ORDER BY id DESC`. No separate timestamp column needed. The ID *is* the timestamp.

**Discord** uses a variant—same idea, different bit layout. **Instagram** did something similar before moving to other approaches. **MongoDB's ObjectId** is 12 bytes (96 bits): timestamp + machine + process + counter. Same philosophy. **Trade-off:** **Clock skew.** If two machines have different clocks, IDs can collide or go out of order. NTP sync is critical. Some systems reserve extra bits for a "logical timestamp" to handle clock drift.

---

## Another Way to See It

A concert. 10 ticket booths. Each booth prints tickets: date (YYYYMMDD) + booth number (01–10) + sequence (001–999). Booth 3 at 2pm: 20250226103001, 20250226103002... Booth 5 at 2pm: 20250226105001... No two booths have the same number. No central coordinator. Tickets sort by time. Snowflake is the same idea—timestamp, machine, sequence.

---

## Connecting to Software

- **Snowflake structure:** 64 bits = **timestamp** (41) + **machine ID** (10) + **sequence** (12). Generate locally. No coordination.
- **Sortable:** IDs increase over time. `ORDER BY id` ≈ chronological order. Efficient for "recent first" queries.
- **Used by:** Twitter (original), Discord, Instagram (variants). **MongoDB ObjectId** is similar (96 bits).
- **Trade-off—clock skew:** If machine A's clock is behind machine B's, A's IDs can be "older" than B's. Collision possible if clocks overlap. **NTP** sync is critical. Some add a "logical clock" to handle backward clock moves.
- **Implementation:** Each service gets a unique machine ID (from config, ZooKeeper, or instance metadata). On each request, increment sequence. If millisecond rolls over, reset sequence. Simple logic. High throughput.

---

## Let's Walk Through the Diagram

```
SNOWFLAKE 64-bit layout
======================

| 1 bit | 41 bits (timestamp) | 10 bits (machine) | 12 bits (sequence) |
|  0   |    milliseconds     |   node ID          |  0-4095 per ms     |
|      |   since epoch       |   (0-1023)         |                    |

Example: 1234567890123456789
- Timestamp: ~year 2009
- Machine: 42
- Sequence: 1234

Generated at same millisecond on same machine:
  ...7890123
  ...7890124
  ...7890125
  (sequence increments)

Different machine:
  ...7890456  (machine 45, seq 6)
  (different machine ID, no collision)
```

---

## Real-World Examples (2-3)

**1. Twitter:** Original Snowflake. Every tweet, every DM, every like—Snowflake ID. Billions per day. 64 bits. Sortable. No coordination. The system that made "distributed unique IDs" mainstream.

**2. Discord:** Uses a variant—slightly different bit allocation. Same idea: timestamp + worker ID + sequence. Every message, every channel, every server. Globally unique. Sortable. Fits in 64 bits.

**3. Sony's PlayStation Network:** Uses a similar approach for entity IDs. Timestamp-based, machine-based. Scale requires distributed ID generation. Snowflake-style is the pattern.

---

## Let's Think Together

**Question:** Your Snowflake implementation has 10 bits for machine ID. You're scaling to 2000 servers. What happens?

**Pause. Think about it...**

**Answer:** **Collision.** 10 bits = 1024 unique values. Two machines could get the same ID. Same timestamp, same machine ID, same sequence = duplicate IDs. Fix: increase machine ID bits (fewer for timestamp or sequence), or use a different allocation. Or use a **coordinator** to assign machine IDs from a pool. Or use **UUID v7** if you need more machines and can afford 128 bits. Plan your bit layout for scale.

---

## What Could Go Wrong? (Mini Disaster Story)

A company implemented Snowflake. Worked great. Then a server's clock drifted backward—NTP glitch, VM restore. That server started generating IDs with "past" timestamps. Collision with IDs already in the database. Duplicate key errors. Writes failed. The fix? **Clock sanity check.** Before accepting a generated ID, compare local time to the last timestamp used. If clock went backward, wait until we "catch up" or use a logical clock. **Twitter's actual implementation** had this. Never trust the clock blindly. Defensive programming saves you.

---

## Surprising Truth / Fun Fact

**Snowflake** wasn't open-sourced by Twitter. The design was described in a blog post. The name stuck. Today, "Snowflake ID" means any timestamp + machine + sequence scheme, not just Twitter's exact implementation. **Snowflake** the data warehouse company is unrelated—different company, same word. Don't confuse them.

---

## Quick Recap (5 bullets)

- **Snowflake:** 64-bit ID = **timestamp** + **machine ID** + **sequence**. Generate locally. No coordination.
- **Sortable:** IDs increase over time. Efficient for "recent first" queries.
- **Used by:** Twitter, Discord, Instagram (variants). MongoDB ObjectId is similar.
- **Trade-off:** **Clock skew** can cause collisions. NTP sync critical. Handle backward clock moves.
- **Bit layout:** Plan for scale. 10-bit machine ID = 1024 machines. Adjust as needed.

---

## One-Liner to Remember

> **Snowflake: 64 bits, timestamp first, machine and sequence after. Unique everywhere. Sortable. No coordinator. Just watch the clock.**

---

## Next Video

You've got unique IDs. Data flows in. But reads and writes hit the same database. At scale, reads dominate. **Read replicas**—separate read-only copies. Write to primary. Read from replicas. Why we separate reads. How it works. Trade-offs. Next: Read replicas and why we separate reads.
