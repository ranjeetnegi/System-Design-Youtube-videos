# LSM Tree: How Write-Heavy Databases Work

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

Imagine a restaurant kitchen. B-trees are like a filing cabinet: every time you add a new recipe, you open a drawer, shuffle papers, reorder, close. Slow. Messy. Random. Now imagine a different kitchen: a whiteboard by the stove. New recipes? Scribble them on the board. Fast. When the board fills up, you copy everything to a binder—in order—and wipe the board clean. That's an LSM tree. Write to memory. Flush to disk in sorted batches. No random shuffling. Just append, append, append.

---

## The Story

**B-trees** are the workhorse of traditional databases. MySQL, PostgreSQL—they use B-trees. Reads are fast. But writes? Every insert might touch multiple pages. **Random I/O**—disk seeks, page splits, rebalancing. At 100,000 writes per second, the disk can't keep up. Random writes are the enemy.

**LSM trees**—**Log-Structured Merge Trees**—flip the script. Writes go to an **in-memory memtable** first. Append-only. No random disk I/O. When the memtable fills, it's flushed to disk as a **sorted SSTable** (Sorted String Table). Sequential write. One big append. Disks are great at sequential writes—10x, 100x faster than random.

But here's the trade-off. Reads might need to check the memtable plus multiple SSTables on disk. **Read amplification**—you might scan several files to find a key. The fix? **Compaction.** Periodically merge SSTables. Fewer, larger files. Reads get faster. But compaction costs CPU and I/O. **Write amplification**—the same logical write might be rewritten multiple times during compaction. You're trading read cost for write cost, and compaction cost for both.

**Cassandra**, **RocksDB**, **LevelDB**, **HBase**—they all use LSM under the hood. Write-heavy? Time-series? Logs? LSM wins. Read-heavy, point lookups? B-tree might still win. Know your workload.

---

## Another Way to See It

A conveyor belt at a factory. B-tree: every new widget stops the belt, workers find the right slot, insert, reorder. Chaos. LSM: new widgets pile up in a bin. When the bin is full, someone sorts them once and adds them to the main shelf in order. The belt never stops. The bin is the memtable. The sorted shelf is the SSTable. Batch the work. Avoid the chaos.

---

## Connecting to Software

- **Memtable:** In-memory buffer. Writes land here first. Fast. When full, flushed to disk as an SSTable.
- **SSTable:** Sorted, immutable file on disk. Key-value pairs in order. No in-place updates—new versions go to newer SSTables.
- **Compaction:** Merge SSTables. **Size-tiered:** merge small files into larger ones. **Leveled:** maintain levels (L0, L1, L2...); each level is 10x the previous. Fewer files = faster reads.
- **Write amplification:** One logical write may be rewritten 5–20x during compaction. Trade-off for fast writes.
- **Bloom filters:** Bit array per SSTable. "Key probably not here" → skip file. Avoid unnecessary disk reads. Critical for read performance.
- **RocksDB:** Facebook's LSM engine. Powers MySQL's MyRocks, Kafka's state store, CockroachDB storage.
- **Cassandra:** LSM + wide-column. Tuned for writes. Used by Netflix, Apple.

---

## Let's Walk Through the Diagram

```
LSM TREE WRITE PATH
===================

  WRITE "key=foo" 
       │
       ▼
  ┌─────────────┐
  │  MEMTABLE   │  ← In-memory, append-only
  │  (sorted)   │
  └──────┬──────┘
         │ memtable full
         ▼
  ┌─────────────┐
  │  SSTable 1  │  ← Flushed to disk (sequential write)
  └─────────────┘
         │
  ┌─────────────┐
  │  SSTable 2  │  ← More writes, another flush
  └─────────────┘
         │
  ┌─────────────┐
  │  SSTable 3  │
  └─────────────┘
         │
    COMPACTION  ← Merge SSTables, reduce read amplification
         │
  ┌─────────────┐
  │  SSTable    │  ← Fewer, larger files
  │  (merged)   │
  └─────────────┘

READ: Check memtable → SSTable 1 → 2 → 3 (or use Bloom filter to skip)
```

---

## Real-World Examples (2-3)

**1. Cassandra at Netflix:** Billions of viewing events. Every play, pause, resume—a write. LSM handles the flood. Writes go to memtable, flush to SSTables. Compaction runs in the background. No random I/O. The database keeps up.

**2. RocksDB in Kafka:** Kafka Streams uses RocksDB for state stores. Windowed aggregations, joins—state is written constantly. LSM's write-optimized design fits. Fast writes. Compaction during low traffic.

**3. LevelDB in Chrome:** Browser storage. IndexedDB under the hood. Writes (bookmarks, cache metadata) are frequent. LSM keeps Chrome responsive. Small footprint. Simple. Reliable.

---

## Let's Think Together

**Question:** You're building a logging system. 1 million log lines per second. Each line: timestamp, level, message. You need to query by time range. B-tree or LSM?

**Pause. Think about it...**

**Answer:** **LSM.** Writes dominate. Append-only. B-tree would thrash with random inserts. LSM: memtable absorbs writes, flush to SSTables. Time-range scans work—SSTables are sorted. Compaction keeps files manageable. RocksDB, Cassandra, or a purpose-built log store (e.g., ClickHouse for analytics) would fit. B-tree would struggle.

---

## What Could Go Wrong? (Mini Disaster Story)

A team ran Cassandra with default compaction. Write load spiked. Compaction couldn't keep up. SSTables piled up—hundreds of files per table. Reads slowed to a crawl. Every read checked dozens of SSTables. Latency went from 5ms to 500ms. P99 hit 2 seconds. The fix? Tune compaction. Switch to **leveled compaction** for better read performance. Add more compaction threads. Or reduce write rate. The lesson: LSM is write-optimized, but compaction is the hidden cost. If compaction falls behind, reads suffer. Monitor compaction lag. Plan for it.

---

## Surprising Truth / Fun Fact

**LSM trees** were invented by Patrick O'Neil in 1996—same era as the B-tree paper's dominance. But LSM didn't take off until **Google's Bigtable** (2006) and **LevelDB** (2011) proved it at scale. One idea, decades later, powers half the write-heavy systems on the planet. Sometimes the right idea just needs the right workload to arrive.

---

## Quick Recap (5 bullets)

- **LSM tree:** Write to **memtable** (memory), flush to **SSTable** (disk). Sequential writes beat random I/O.
- **Trade-off:** Write amplification (compaction) vs read amplification (many files). Tune for your workload.
- **Compaction:** Size-tiered or leveled. Merges SSTables. Reduces read cost. Costs CPU and I/O.
- **Bloom filters:** Skip SSTables that don't contain a key. Critical for read performance.
- **Used in:** Cassandra, RocksDB, LevelDB, HBase. Write-heavy, time-series, logs.

---

## One-Liner to Remember

> **LSM: write to memory, flush to disk in sorted batches. No random I/O. Compaction is the price you pay for fast writes.**

---

## Next Video

LSM trees use **Bloom filters** to skip SSTables during reads. "This key probably isn't here"—skip the file. No disk read. But what exactly is a Bloom filter? A bit array. Hash functions. False positives, never false negatives. The fastest way to say "probably yes." Next: Bloom filters.
