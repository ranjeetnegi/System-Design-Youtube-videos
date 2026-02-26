# Design Problem: Booking / Ticketing System

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

Ten thousand people. One concert. One click. "Book now." They all hit the system at the same time. Seat 7A. Who gets it? If you're not careful, five people get it. Double booking. Chaos. Refunds. Lawsuits. A booking system's job: **no double booking**. Real-time availability. Fairness. Let's design it.

---

## The Story

**Requirements.** **No double booking**—one seat, one owner. Invariant. **Real-time availability**—users see what's free *now*. Not stale. **Concurrent requests**—thousands hitting the same seat. Handle the thundering herd. **Payment integration**—reserve, then pay. Or pay, then confirm. Different flows. **Fairness**—first-come-first-served? Or lottery? Design choice.

**Pessimistic vs optimistic locking.** **Pessimistic:** Lock the seat when someone views it. "Seat 7A is mine for 10 minutes." Others see it as taken. Pro: no collision. Con: locks tie up inventory. User abandons? Seat locked for nothing. **Optimistic:** Assume no conflict. User clicks "book." You try. If someone else got it first, you fail. Retry with another seat. Pro: no locks. Better availability. Con: user might try several times. **Hybrid:** Short hold. "Reserved for 10 minutes. Pay to confirm." Release if no payment. This is the **seat hold** or **reservation** pattern. BookMyShow, Ticketmaster, airlines use it.

**Seat hold pattern.** User selects seat → System creates **temporary reservation** (hold) → Timer starts (e.g., 10 min) → User pays → Hold converts to **confirmed booking** → Seat is theirs. User abandons? Timer expires → Hold released → Seat back in pool. The hold is short. Reduces lock duration. Lets you oversell holds slightly (some will abandon) but never oversell confirmed seats.

**Payment integration.** Two phases: reserve, then pay. **Idempotency** is critical. User clicks "pay" twice. Network retry. Payment succeeds once. Booking created once. Use **idempotency keys**. Payment fails? Release hold. Don't confirm. **Eventual consistency** between payment service and booking service—use **saga** or **outbox** if needed.

---

## Another Way to See It

A restaurant. Walk-in. "Table for two?" Host says, "I'll hold it for 15 minutes while you park." That's a hold. You come back, you're seated. You don't come back, table goes to the next person. The hold prevents double-booking (they're not giving your table away) but limits waste (15 min, not all night). Booking systems work the same. Hold. Pay. Confirm. Or release.

---

## Connecting to Software

- **Pessimistic locking:** `SELECT ... FOR UPDATE`. Lock row. Others wait. Simple but blocks.
- **Optimistic locking:** Version column. `UPDATE SET ... WHERE version = X`. If version changed, conflict. Retry.
- **Seat hold:** Temporary reservation. TTL (e.g., 10 min). **Redis** with TTL or DB with `expires_at`. Background job releases expired holds.
- **Idempotency:** Payment + booking. Idempotency key per user session. "First request wins." Prevents double charge, double booking.
- **Real tools:** **Ticketmaster**, **BookMyShow**, **Stripe** (payments), **Redis** (holds, rate limiting). **PostgreSQL** advisory locks for critical sections.

---

## Let's Walk Through the Diagram

```
BOOKING FLOW (Seat Hold Pattern)
================================

User                API                 DB/Redis           Payment
  |                   |                     |                  |
  |-- Select seat --->|                     |                  |
  |                   |-- Create hold ----->| (hold 10 min)    |
  |                   |<-- Hold ID ---------|                  |
  |<-- "Reserved" ----|                     |                  |
  |                   |                     |                  |
  |-- Pay ----------->|                     |                  |
  |                   |-----------------------> Charge -------->|
  |                   |<----------------------------------------|
  |                   |-- Confirm booking -->| (hold → booking) |
  |                   |-- Release hold ----->|                  |
  |<-- "Confirmed" ---|                     |                  |

If user abandons:
  Timer expires → Background job releases hold → Seat back in pool
```

---

## Real-World Examples (2-3)

**1. BookMyShow (India):** Movie tickets. High concurrency during blockbuster releases. Seat hold for 8-10 minutes. Payment gateway integration. They've written about handling 10K+ concurrent bookings. Hold + payment + confirm. No double booking.

**2. Ticketmaster:** Concert tickets. Infamous for sellouts in minutes. Queue system. Hold. Payment. Same pattern. Scale: millions of users, thousands of seats. Geographically distributed. They use sophisticated queueing and hold management.

**3. Airline booking:** Similar. Seat selection. Hold. Payment. Extra: seat maps, multiple passengers, ancillary (bags, meals). Same core: no double booking. Hold. Confirm.

---

## Let's Think Together

**Question:** Hold duration: 5 minutes or 15 minutes? Trade-off?

**Pause. Think about it...**

**Answer:** **Shorter is usually better.** 5 min: less inventory tied up. More seats available for others. Faster churn. But user might not finish payment in time. 15 min: more comfortable for user. But more seats locked. For high-demand events, 5 min is common. For low-demand, 10-15 min is fine. A/B test. Also consider: **abandonment rate**. If 80% abandon, short holds mean more retries, but more availability. If 20% abandon, longer holds are less wasteful. Typical: 8-10 minutes. Adjust based on data.

---

## What Could Go Wrong? (Mini Disaster Story)

A ticketing company used optimistic locking. No holds. User A and User B both selected seat 7A. Both clicked "pay." Both payments went through. Both got confirmations. One seat. Two owners. Double booking. Refunds. Angry customers. The fix: they introduced **holds**. Select seat → 10 min hold → pay → confirm. Only the holder can pay for that seat. Collision eliminated. Lesson: for high-contention inventory, optimistic locking alone is risky. Holds create a "claim" that serializes the critical section. Use them.

---

## Surprising Truth / Fun Fact

Some booking systems **oversell** intentionally. Airlines do it. No-shows are common. They sell 105% of seats. Most flights, everyone shows. Occasionally, they need to bump. They compensate. The math works. For concerts? Usually no oversell—hard capacity. For flights? Oversell is a business decision. Design supports both: strict (no oversell) or probabilistic (oversell with bump logic).

---

## Quick Recap (5 bullets)

- **No double booking:** Invariant. Use locks or holds. Never violate.
- **Seat hold pattern:** Temporary reservation. TTL. Pay to confirm. Release if abandoned.
- **Pessimistic vs optimistic:** Pessimistic = lock. Optimistic = try, retry on conflict. Holds = hybrid.
- **Idempotency:** Payment + booking. Idempotency keys. Prevent double charge, double confirm.
- **Scale:** Holds in Redis. Background job for expiry. Queue for high-demand. Fairness policy.

---

## One-Liner to Remember

> **Hold, then pay, then confirm. Release if abandoned. Idempotency for payments. No double booking—ever.**

---

## Next Video

Booking done. Next: **file sync service**—Dropbox-style. Sync across devices. Offline support. Conflict resolution. Chunking. Metadata. Notification. It's a step up in complexity. Let's design it.
