# Cache-Aside Pattern: Lazy Loading

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

Your friend gives you their new phone number. You don't memorize all 300 contacts in your phone. You only save numbers when someone actually calls or texts you. First time? You look them up: "Hey, what's your number?" They tell you. You save it. Next time they call? Their name pops up instantly. You didn't pre-load every number in the city directory. Only the ones you actually needed. That's cache-aside.

---

## The Story

Aarav just started a new job. He needs to call different teams constantly. Finance. HR. IT support. Legal. Every time he needs a number, he looks it up in the company directory—a big PDF buried three clicks deep in the intranet. Find the department. Scroll. Find the name. Copy the number. Dial. Takes two minutes each time. Exhausting.

One day he tries something different. Before looking up the directory, he checks his phone's recent calls and saved contacts. Is the number already there? Not this time. So he digs through the directory. Finds the IT support number. Dials. But here's the key: after the call, he saves the number in his phone. "IT Support — Ext 4455." The next time he needs IT? He taps the saved contact. Instant. No directory. No scrolling. No waiting.

He doesn't save every number in the company directory. That would be hundreds of entries he'll never call. He only saves what he's actually dialed. Lazy. On-demand. His contacts list fills only when he actually needs someone. No guessing. No bulk imports. Just what he uses. When he uses it.

That's exactly how cache-aside works. The application checks the cache first. Hit? Return cached data. Miss? Go to the database, fetch the data, store it in the cache, return it. The cache fills only when someone actually asks for something. Simple. Efficient. Proven.

---

## Another Way to See It

Cooking. First time making a new recipe? You look it up—maybe in a cookbook or online. You write the key steps on a sticky note and slap it on the fridge. Next time you want that dish? You don't open the cookbook. You don't search the web. The sticky note is right there. You filled your "cache" only when you actually needed the recipe. Lazy loading. The cookbook is your database. The sticky note is your cache. Same pattern. No different.

---

## Connecting to Software

**The flow in code-like pseudocode:**

```
function get(key):
    value = cache.get(key)
    if value != null:
        return value          // CACHE HIT - done!
    // CACHE MISS
    value = database.query(key)
    cache.set(key, value, ttl=300)
    return value
```

**Step by step:** The application receives a read request. It checks the cache for the key. Hit? Return the cached value immediately. Miss? Query the database. Write the result to the cache. Return the data to the user. That's it.

Why "cache-aside"? Because the cache sits to the side. The application manages it. Not the database. Not some automatic sync layer. The app decides when to read, when to write. Full control. The database doesn't know a cache exists. It's the application's responsibility to keep them in sync—or at least, to populate the cache on demand.

**Lazy loading** means we don't pre-fill the cache at startup. We fill it on first request. Cold start? All misses initially. As traffic grows, the cache warms up. Popular data stays. Unpopular data never gets cached. The cache naturally reflects real usage. Efficient. Self-tuning.

---

## Let's Walk Through the Diagram

```
    READ REQUEST
         │
         ▼
    ┌─────────────┐
    │  Check      │
    │  Cache      │
    └──────┬──────┘
           │
     ┌─────┴─────┐
     │           │
   HIT         MISS
     │           │
     ▼           ▼
  Return     Query DB
  cached     ┌────┴────┐
  value      │         │
             ▼         ▼
         Write to   Return
         cache      value
```

**Step 1:** The app receives a read request. It checks the cache first. This is the critical branch point.

**Step 2:** Hit? Return immediately. No database involved. Sub-millisecond response. Miss? Continue to step 3.

**Step 3:** Query the database for the key. This is the slow path—network round-trip, disk I/O, whatever the DB costs.

**Step 4:** Store the result in the cache with a TTL. The next reader benefits. We're being considerate.

**Step 5:** Return the data to the caller. Same response either way. Different path.

This is the most common caching pattern in production. Redis plus cache-aside is the default for Twitter, Instagram, Uber, Netflix. Billions of requests. One pattern.

---

## Real-World Examples (2-3)

**Example 1 — User profile:** User A reads their profile. Cache miss. Data fetched from DB. Cached. User B reads the same profile a second later? Cache hit. One DB query serves dozens—or hundreds—of reads. The first user "pays" the cost. Everyone else gets speed.

**Example 2 — Product catalog:** E-commerce product page. Product ID 12345. First user loads the page. Miss. DB query. Cache stores the full product object. Next 10,000 users? Cache hit. Database breathes. One query. Ten thousand fast responses.

**Example 3 — News headline:** Breaking news article. First reader triggers the cache fill. Millions of subsequent readers get the cached version. Until it expires or gets invalidated. That first hit is expensive. The rest? Almost free.

---

## Let's Think Together

User A reads their profile. Cache miss. Data is fetched from the DB and cached. User B reads the same profile immediately after. Cache hit. Fast. Good.

But what if User A updates their profile between those two reads? User B still gets the old cached version. Stale data. How long until User B sees the update? Until the cache entry expires (TTL) or someone explicitly invalidates it. Cache-aside doesn't solve consistency. It solves performance. Consistency is a separate problem. You need invalidation strategies. TTL. Or invalidate-on-write. The pattern is simple. The consequences require design.

---

## What Could Go Wrong? (Mini Disaster Story)

Riya's team launches a flash sale. Product prices drop by 50%. The database is updated. Done. The product pages? Cached. Cache-aside filled them an hour ago. TTL is 30 minutes.

For the next 30 minutes, thousands of users see the old price. Wrong. Confusing. "Why does the cart show a different price than the product page?" Support flooded. Complaints. Lost trust. The cache served stale data. No one thought to invalidate on price change. The pattern worked—the cache was fast. But correctness? Forgotten. Lesson: cache-aside is lazy and simple. You must handle invalidation yourself. Every write path. Every update. Or pay the price.

---

## Surprising Truth / Fun Fact

Redis plus cache-aside is the most used caching setup in the world. When you scroll your feed on Twitter, like a post on Instagram, or check your ride status on Uber—cache-aside is working behind the scenes. Billions of requests per day. One pattern. No magic. Just: check cache, miss, fetch, store, return.

---

## Quick Recap (5 bullets)

- **Cache-aside** = check cache first → miss? → read from DB → store in cache → return.
- **Lazy loading** = no pre-fill. Cache fills only when data is actually requested.
- **Cache sits to the side** = application manages it. Not the database.
- **Cold start** = all misses at first. Cache warms up as traffic grows.
- **Most common pattern** = default choice for Redis, Memcached, and production systems.

---

## One-Liner to Remember

*Cache-aside: Check cache first. Miss? Fetch from DB, cache it, return. Only cache what people actually ask for.*

---

## Next Video

Next: Write-through cache—when every write goes to both cache and database. Never stale. But at what cost?
