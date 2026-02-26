# Compare-and-Swap (CAS): Lock-Free Concurrency

## Video Length: ~4-5 minutes | Level: Senior-Staff

---

## The Hook (20-30 seconds)

A vending machine. You put in a dollar. The machine checks: "Do I have exactly one dollar?" Yes. It swaps: takes your dollar, gives you the snack. Atomic. One operation. If someone else put in a dollar a millisecond earlier, the machine would have two dollars—different state. Your "compare" would fail. "Expected one dollar, found two." No swap. You retry. That's compare-and-swap. No lock. Just: "if it's X, make it Y. All or nothing." The CPU does this in one instruction.

---

## The Story

**Compare-and-Swap (CAS)** is an atomic operation. Three inputs: memory location, expected value, new value. The CPU does: "If the value at this location equals expected, write new. Otherwise, do nothing." Returns success or failure. All in one indivisible step. No other CPU can slip in between. **Lock-free.** No mutex. No blocking. Just a single hardware instruction.

Why does it matter? **Locks have overhead.** Acquire lock. Wait if contested. Release. Context switches. Priority inversion. **CAS** avoids all that. You read the value. You compute the new value. You CAS: "if it's still what I read, write my new value." Failed? Someone else changed it. Retry. **Optimistic concurrency at the hardware level.** Most of the time, no conflict. One instruction. Done.

**Where you see it:** **Java** `AtomicInteger.incrementAndGet()`—uses CAS under the hood. **Go** `sync/atomic` package. **Redis** `WATCH`/`MULTI`/`EXEC`—optimistic locking; not true CAS but similar idea. **etcd**—compare-and-swap for distributed state. **Lock-free data structures**—queues, stacks—built on CAS. No locks. Just retry until you win.

**The ABA problem:** Value was A. You read it. Someone changes it to B. Someone changes it back to A. You CAS: "if A, write X." It succeeds! But the value had a history—A→B→A. You didn't know. For pointers, this can cause bugs. **Solution:** Use a version number or stamp. Compare (value, version). ABA can't happen if version always increases. **Java** `AtomicStampedReference` does this.

---

## Another Way to See It

A whiteboard with a number. Everyone can read and write. **Lock approach:** "I'm writing. Wait." Everyone queues. **CAS approach:** You read "42." You want to write "43." You say: "If it's still 42, write 43." One person does it. Succeeds. The next person reads "43." Tries "if 42, write 44." Fails. Retries with "if 43, write 44." Succeeds. No waiting. No lock. Just atomic check-and-update. First to match wins.

---

## Connecting to Software

- **CAS operation:** `CAS(memory, expected, new)` → success/fail. Atomic. Single CPU instruction (e.g., `CMPXCHG` on x86).
- **Java:** `AtomicInteger`, `AtomicLong`, `compareAndSet()`. `incrementAndGet()` is a CAS loop. Lock-free counters.
- **Redis:** `WATCH` key. `MULTI`. Operations. `EXEC`. If key changed between WATCH and EXEC, EXEC fails. Optimistic. Retry. Similar to CAS for key-value.
- **etcd:** `CompareAndSwap` in the API. Distributed CAS. Used for leader election, distributed locks.
- **Lock-free structures:** Concurrent queues (Michael-Scott queue), stacks. Built on CAS. No mutex. High throughput, no blocking.
- **ABA problem:** Value A→B→A. CAS thinks nothing changed. Use versioned CAS (stamp, sequence number) when it matters.

---

## Let's Walk Through the Diagram

```
    THREAD A                    MEMORY                    THREAD B
        │                          │                          │
        │  read: value = 5          │                          │
        │  compute: new = 6         │                          │
        │                          │  read: value = 5          │
        │                          │  compute: new = 7         │
        │  CAS(addr, 5, 6)         │                          │
        │  ──────────────────────> │  value == 5? YES.        │
        │                          │  write 6. SUCCESS.        │
        │                          │ <──────────────────────  │
        │                          │  CAS(addr, 5, 7)         │
        │                          │  value == 5? NO (it's 6) │
        │                          │  FAIL. Retry.            │
        │  done ✓                  │                          │  read 6, CAS(6,7)...
```

**Key:** Only one CAS succeeds. The other fails, retries with fresh read. No lock. No wait. Atomic swap.

---

## Real-World Examples (2-3)

**Example 1 — Atomic counter:** Request ID generator. Every request: `id = atomic.incrementAndGet()`. CAS under the hood. Millions of requests per second. No lock. No bottleneck. Java, Go, C++—all use this pattern.

**Example 2 — Redis WATCH:** You WATCH balance. You read it. You MULTI. You DECR. You EXEC. If balance changed (another client modified it), EXEC returns nil. You retry. Optimistic. CAS-like. Used for distributed rate limiting, inventory checks.

**Example 3 — etcd leader election:** Compare-and-swap on a "leader" key. "If no leader, I'm the leader." CAS. Winner gets the lease. Others retry or become followers. Distributed systems. No central lock server. CAS in the data store.

---

## Let's Think Together

You need a thread-safe counter. 10 threads. Each increments 1 million times. Lock vs CAS?

Pause and think.

CAS. Or a lock. At low contention, both work. At high contention, a lock causes threads to block and wait. CAS causes retries—but no blocking. Often CAS wins: threads spin, retry, eventually succeed. No context switch. No sleep. For a simple counter, `AtomicInteger` (CAS) typically beats `synchronized` (lock) in benchmarks. Use CAS for simple atomic updates. Locks for complex critical sections.

---

## What Could Go Wrong? (Mini Disaster Story)

A team built a lock-free queue using CAS. Worked in testing. In production: rare ABA. A pointer was freed and reallocated. Same address. Different object. CAS saw same value. Succeeded. Corrupted the queue. Crashes. Data loss. Lesson: ABA is real when reusing memory. Use `AtomicStampedReference` or hazard pointers. Or avoid ABA-prone patterns. CAS is powerful. ABA is the subtle trap. Know when to use versioned CAS.

---

## Surprising Truth / Fun Fact

The CAS instruction has been in x86 since the 80486 (1989). It's the foundation of most lock-free algorithms. Java's `ConcurrentHashMap` uses CAS for fine-grained updates. Go's sync package uses it. The hardware has had this for decades. Software is still learning to use it well.

---

## Quick Recap (5 bullets)

- **CAS** = atomic "if value == expected, write new." Single CPU instruction. Lock-free.
- **Use case:** Counters, lock-free queues, optimistic concurrency. No mutex. Retry on failure.
- **Java** `AtomicInteger`, **Go** `atomic`, **Redis** WATCH/MULTI—all CAS or CAS-like.
- **ABA problem:** Value A→B→A. CAS thinks unchanged. Use versioned CAS when it matters.
- **Trade-off:** CAS = no blocking, retries. Lock = blocks, simpler. CAS wins for simple atomic ops at scale.

---

## One-Liner to Remember

> Compare-and-Swap: "If it's X, make it Y." One atomic instruction. No lock. Retry if someone else won. Optimistic concurrency at the hardware level.

---

## Next Video

Your service needs to call another service. But its IP changes. Pods come and go. How do you find it? **Service discovery**—how services find each other in a dynamic world. Consul, etcd, Kubernetes. Next up. See you there.
