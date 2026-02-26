# Design Problem: Typeahead / Autocomplete

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

You type "netfl" and before you finish, "Netflix" appears. You type "syst" and "system design" pops up. Instant. Ranked. Sometimes it even fixes your typo. How does that work? Millions of users. Billions of queries. Sub-100-millisecond latency. It's not magic—it's a well-designed system. Let's break it down.

---

## The Story

**Requirements.** **Low latency**—under 100ms. Users type fast. Suggestions must appear before they finish the next keystroke. **Ranked suggestions**—"Netflix" before "networking" when you type "net." Popularity matters. **Handle typos**—"gooogle" might still suggest "Google." Fuzzy matching. **Scale**—millions of queries per second. Google, YouTube, Amazon all do this.

**Core data structure: Trie.** A **trie** (prefix tree) stores strings by character. Root → n → e → t → f → l → i → x. Type "net" and you're at a node. All descendants are completions: "netflix," "network," "networking." Trie gives you **prefix lookup** in O(k) where k = length of prefix. Fast. But a raw trie doesn't rank. "Network" might appear before "Netflix" alphabetically. You need **scores** at each node—popularity, recency, click-through rate. When you reach "net," you don't return all descendants—you return the **top N by score**.

**Pre-computed suggestions.** For hot queries—"netflix," "weather," "system design"—you can **pre-compute** top suggestions. Store in cache. User types "net" → cache hit → return cached list. No trie traversal. No ranking computation. Just lookup. **Caching popular queries** is huge. 80% of traffic might be 20% of prefixes.

**Handling typos.** **Edit distance** (Levenshtein)—expensive. **Fuzzy trie**—store common misspellings. Or **n-grams**—index "gooogle" as "goo," "ooo," "oog," "ogl," "gle." Query "google" overlaps. Retrieval + re-rank. Trade-off: recall vs latency. Simple prefix works for most. Fuzzy adds complexity.

---

## Another Way to See It

A dictionary. You flip to "N." Then "E." Then "T." You're in the "net" section. All words starting with "net" are there. The trie is that structure—optimized for "give me everything after this prefix." The ranking is like the dictionary putting the most looked-up words first. Pre-computation is like having a "top 10 net- words" sticky note. Instant.

---

## Connecting to Software

- **Trie (prefix tree):** O(k) prefix lookup. Store strings by character. Each node = prefix. Children = possible next characters. Add scores for ranking.
- **Pre-computed suggestions:** For hot prefixes, pre-compute top N. Store in **Redis** or **Memcached**. Key = prefix. Value = ranked list. Sub-millisecond lookup.
- **Caching:** **LRU cache** for recent queries. **CDN** for static suggestion APIs if applicable. **Edge caching** for low latency.
- **Ranking signals:** Query frequency, click-through rate, recency, user context. **Machine learning** for personalization (advanced).
- **Real tools:** **Elasticsearch** (completion suggester), **Redis** (sorted sets, prefix search), **Google** (proprietary), **Algolia** (search-as-a-service with autocomplete).

---

## Let's Walk Through the Diagram

```
TRIE + RANKING
==============

         (root)
           |
           n
           |
           e
           |
           t  ← User types "net", we're here
          / \
         f   w
         |   |
         l   o
         |   |
         i   r
         |   |
         x   k
         |   |
      (EOW) (EOW)
      
      Netflix    network
      score:95   score:70

At "net" node: 
  - Get top-k descendants by score
  - Return: [Netflix(95), network(70), networking(60)]
  - Limit to 10. Sorted by score.

CACHE LAYER (for hot prefixes):
  "net" → [Netflix, network, networking, ...]
  "sys" → [system design, system, ...]
  Cache hit: return immediately. No trie traversal.
```

---

## Real-World Examples (2-3)

**1. Google Search:** Autocomplete as you type. Handles typos. Personalized (your history). Billions of queries. They use massive distributed tries, pre-computation, and ML ranking. Latency budget: tens of milliseconds.

**2. YouTube Search:** Similar. Suggests videos, channels, topics. Pre-computed for trending. Real-time for long-tail. Caching at edge. Sub-100ms.

**3. Amazon:** Product search autocomplete. "iphone" → "iPhone 15," "iPhone case," etc. Ranked by sales, relevance. Trie + cache + ranking pipeline.

---

## Let's Think Together

**Question:** You're building autocomplete for a dictionary app. 100K words. 1000 queries per second. Do you need a cache for popular prefixes?

**Pause. Think about it...**

**Answer:** **Probably yes.** 100K words is small—a trie fits in memory. 1000 QPS is moderate. But here's the thing: **query distribution is skewed**. "a," "the," "go," "run"—a few prefixes get most traffic. Caching "a" (top 10 words starting with "a") might serve 30% of queries. One trie traversal to warm cache. Then thousands of cache hits. Low cost. High payoff. Even at 1000 QPS, caching hot prefixes reduces load and improves p99 latency. Add it. It's simple. It helps.

---

## What Could Go Wrong? (Mini Disaster Story)

A startup built autocomplete with a trie. No cache. Worked fine at 100 QPS. They launched. Viral. 100,000 QPS. The trie was in one server. Every query hit it. CPU spiked. Latency went from 20ms to 2 seconds. Users saw suggestions appear *after* they'd finished typing. Terrible UX. The fix: they added **Redis** cache for top 1000 prefixes. 80% of queries became cache hits. Trie load dropped 5x. Latency recovered. Lesson: design for the hot path. Most queries are repetitive. Cache them. Don't assume uniform distribution.

---

## Surprising Truth / Fun Fact

Google's autocomplete avoids certain suggestions—inappropriate content, hate speech, dangerous queries. There's a **blocklist** and **ML moderation** layer. The technical system (trie, ranking) is one part. The policy layer is another. Real-world autocomplete isn't just "return top by score." It's "return top by score, filtered by policy." Something to consider in design discussions.

---

## Quick Recap (5 bullets)

- **Trie:** Prefix tree. O(k) lookup. Store strings by character. Add scores for ranking.
- **Pre-compute:** Hot prefixes → cache top N. Sub-ms lookup. Huge latency win.
- **Caching:** Query distribution is skewed. Cache popular prefixes. 80/20 rule.
- **Ranking:** Popularity, CTR, recency. Scores at trie nodes. Return top-k.
- **Typos:** Fuzzy matching possible but expensive. Start with prefix. Add fuzzy if needed.

---

## One-Liner to Remember

> **Trie for prefix lookup. Scores for ranking. Cache for hot queries. Sub-100ms or users type faster than you respond.**

---

## Next Video

Autocomplete done. Next: **booking system**. Seats. Flights. Concerts. No double booking. Real-time availability. Thousands of concurrent users grabbing the same seat. How do you prevent chaos? Pessimistic vs optimistic locking. Seat hold. Payment integration. Let's design it.
