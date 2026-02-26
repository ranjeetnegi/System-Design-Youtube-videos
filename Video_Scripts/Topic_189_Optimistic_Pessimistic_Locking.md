# Optimistic vs Pessimistic Locking

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

Two people want the last concert ticket. **Pessimistic:** The moment you walk up, the clerk locks the ticket. "It's yours while you decide." Nobody else can touch it. Safe. But if you take ten minutes to decide, everyone else waits. **Optimistic:** The clerk says "we have one left." You and another person both rush to pay. First to complete wins. The second gets "sorry, it's gone." No locking. But someone always loses and has to retry. Two strategies. Different trade-offs.

---

## The Story

**Pessimistic locking** means you lock the resource before you read or write. "I'm going to change this. Nobody else touch it." In databases: `SELECT ... FOR UPDATE`. You acquire a lock. You read. You modify. You commit. The lock releases. Anyone else trying to lock the same row waits. Or times out. Safe. No conflicts. But: if locks are held long, throughput drops. Everyone queues. Deadlocks possible. Best when **contention is high**—many people fighting for the same row. Ticket booking. Seat selection. Inventory deduction.

**Optimistic locking** means you don't lock. You read. You remember the version—or a timestamp, or a checksum. You modify. When you write, you check: "did anyone change this since I read it?" If yes: conflict. Retry. If no: write succeeds. No locks. High throughput when **contention is low**. Most reads don't conflict. Retries are rare. Best for: collaborative editing (Google Docs), profile updates, most CRUD. When conflicts are rare, optimistic wins.

**The key question:** How often do two transactions touch the same row? **High contention** (auction last second, ticket sale, bank transfer between same accounts)? Pessimistic. Lock it. **Low contention** (millions of users, each editing their own profile)? Optimistic. Retry on conflict.

**Implementation:** Pessimistic = `SELECT FOR UPDATE` or equivalent. Optimistic = add a `version` column. Read version. Update with `WHERE version = X`. If zero rows updated, someone else won. Retry.

---

## Another Way to See It

Shared document. **Pessimistic:** You lock the paragraph before editing. Others see "locked." They wait. You finish. Unlock. **Optimistic:** Everyone edits. When you save, the system checks: "did anyone else save this section?" If yes, merge or ask you to refresh. Google Docs uses optimistic—merge, not lock. High collaboration. Low blocking.

---

## Connecting to Software

- **Pessimistic locking:** `SELECT ... FOR UPDATE` in SQL. Holds row lock until commit. **Java** `synchronized`, **ReentrantLock**. Use when: high contention, critical sections (money, inventory), short transactions.
- **Optimistic locking:** Version column. `UPDATE table SET x=1, version=version+1 WHERE id=1 AND version=5`. Zero rows? Conflict. Retry. **JPA** `@Version`. **Django** `select_for_update` vs `F()` expressions. Use when: low contention, read-heavy, collaborative editing.
- **Real-world:** Ticket booking (Ticketmaster) = pessimistic. Last seat? Lock it. Google Docs = optimistic. Merge on save. Bank transfer = often pessimistic for same-account transfers. E-commerce cart = often optimistic. Conflicts rare.
- **Trade-off:** Pessimistic = safe, blocks others. Optimistic = fast, retry on conflict. Measure contention. Choose.

---

## Let's Walk Through the Diagram

```
    PESSIMISTIC                          OPTIMISTIC
    ------------                         ----------

    Tx A: SELECT FOR UPDATE (lock)       Tx A: SELECT (version=5)
    Tx B: SELECT FOR UPDATE (waits...)   Tx B: SELECT (version=5)
    Tx A: UPDATE, COMMIT (unlock)        Tx A: UPDATE WHERE version=5 ✓
    Tx B: (gets lock) SELECT, UPDATE     Tx B: UPDATE WHERE version=5
         COMMIT                              → 0 rows (conflict!)
                                             → RETRY with fresh read
```

**Left:** B waits. Serialized. Safe. **Right:** Both proceed. A wins. B retries. No waiting. Higher throughput when conflicts are rare.

---

## Real-World Examples (2-3)

**Example 1 — Ticket booking:** Concert. 100 seats. 10,000 people at 10:00. Pessimistic. When you click "reserve," the system locks that seat. You have 10 minutes to pay. Nobody else gets it. Lock is necessary. Optimistic would mean thousands of retries. Chaos.

**Example 2 — Google Docs:** Millions of users. Each document has a few collaborators. Optimistic. You type. I type. We both save. Operational transform or CRDT merges our changes. Locking would kill collaboration. Optimistic + merge. Standard.

**Example 3 — Bank transfer (same account):** Two debits from the same account at once. Pessimistic. Lock the account row. Process one. Then the other. Avoid overdraft. High contention on that one row. Pessimistic wins.

---

## Let's Think Together

You're building a wiki. Users edit pages. Usually one editor at a time. Occasionally two. Optimistic or pessimistic?

Pause and think.

Optimistic. Most edits are solo. Conflicts rare. When two people edit the same paragraph, one gets a conflict. "Someone else changed this. Refresh and try again." Or merge. Acceptable. Locking the whole page would block everyone. Optimistic. Retry or merge. Good enough.

---

## What Could Go Wrong? (Mini Disaster Story)

A startup used optimistic locking for flash sale inventory. 10,000 users. 100 items. Every user read "5 left." Every user tried to decrement. 9,995 got "version conflict." Retry. Read again. "0 left." Retry. "0 left." Infinite retry loops. Database hammered. Site crashed. Lesson: high contention = pessimistic. Or use a different approach—reserve-then-confirm, queue, etc. Optimistic assumes low conflict. Flash sales are high conflict. Wrong tool.

---

## Surprising Truth / Fun Fact

Amazon's DynamoDB uses optimistic locking by default with conditional writes. You specify a condition: "update only if version = X." Conflict? ConditionalCheckFailedException. Retry. No server-side locks. Scales to millions of writes. Optimistic at the database level.

---

## Quick Recap (5 bullets)

- **Pessimistic** = lock before read/write. `SELECT FOR UPDATE`. Safe. Blocks others. Use when contention is high.
- **Optimistic** = read version, write with version check, retry on conflict. No lock. Use when contention is low.
- **Contention** = how often two transactions touch the same row. High = pessimistic. Low = optimistic.
- **Implementation:** Pessimistic = DB locks. Optimistic = version column + conditional update.
- **Real-world:** Tickets, inventory, same-account transfers = pessimistic. Docs, profiles, most CRUD = optimistic.

---

## One-Liner to Remember

> Pessimistic: lock first, nobody else touches it. Optimistic: hope for the best, retry on conflict. High contention? Lock. Low contention? Retry.

---

## Next Video

What if you could do "check and swap" in a single atomic instruction? No locks. Just: "if the value is X, make it Y." **Compare-and-Swap (CAS)**—lock-free concurrency at the hardware level. Java AtomicInteger. Redis WATCH. Next up. See you there.
