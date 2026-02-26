# Design Problem: Web Crawler

## Video Length: ~4-5 minutes | Level: Senior-Staff

---

## The Hook (20-30 seconds)

The web has billions of pages. Google indexes them. How? A **crawler** discovers URLs, downloads pages, extracts links, repeats. Sounds simple. It's not. Politeness—don't overwhelm servers. Deduplication—don't crawl the same page twice. Scale—billions of URLs. Priority—which to crawl first? Googlebot, Common Crawl, Bingbot—they solve this. Let's design it.

---

## The Story

**Requirements.** **Discover URLs**—start from seed URLs. Extract links from pages. Recursively discover. **Download pages**—fetch HTML. Respect **robots.txt**. **Politeness**—rate limit per domain. Don't DDoS. **Scale**—billions of pages. Distributed. **Deduplication**—URL seen? Skip. Content duplicate? Skip (optional). **Priority**—crawl important pages first. Not just BFS.

**URL frontier.** The **URL frontier** is the queue of URLs to crawl. Not a simple FIFO. **Per-domain queues**—one queue per domain. Crawl one URL from domain A, then one from domain B. Prevents hammering one site. **Priority**—important URLs first. Importance: PageRank, sitemap, recency. **BFS** (breadth-first) gives broad coverage. **Priority queue** gives important-first. Hybrid: BFS within domain, priority across domains. **Bloom filter** or **distributed set** for "already seen" URLs. Billions of URLs—can't store in memory. **Bloom filter** for fast "maybe seen." **Persistent storage** for confirmed. Or **sharded key-value store** (URL → status).

**Robots.txt.** Before crawling a domain, fetch **robots.txt**. "Disallow: /admin." "Crawl-delay: 1." Respect it. Legal and ethical. Cache robots.txt. Re-fetch periodically. **Politeness**—max N requests per second per domain. Typically 1-2. Spread load. Be a good citizen.

**Deduplication.** **URL deduplication**—normalize URL (lowercase, remove fragment, sort query params). Same URL? Skip. **Content deduplication**—two URLs, same content? **Simhash** or **minhash** for near-duplicate detection. Saves storage and processing. Optional but valuable at scale.

**Distributed crawling.** One crawler can't do billions. **Shard URLs** by domain or hash. Each worker crawls its shard. **Coordination**—distributed queue (Kafka, SQS). Workers pull URLs. Push new URLs back. **Fault tolerance**—URLs are retried. Idempotent. Crawl same URL twice? Overwrite. OK. **Backpressure**—if downloaders are slow, don't flood the queue. Throttle.

---

## Another Way to See It

A library. You have a list of books to read. You read one. It references ten others. You add them to your list. You pick the next book. But you don't read the same book twice—you check your "read" list. And you don't read 100 books from the same author in a row—you rotate. Spread the load. The URL frontier is your reading list. Deduplication is your "read" list. Politeness is rotating authors. Crawling is reading and extracting references.

---

## Connecting to Software

- **URL frontier:** Priority queue. Per-domain queues. **Redis** sorted sets, **Kafka** topics, **SQS** with FIFO. Sharded by domain.
- **Deduplication:** **Bloom filter** (probabilistic, fast). **RocksDB**, **Cassandra** for persistent URL store. **Simhash** for content.
- **Downloader:** **HTTP client** (respect User-Agent, rate limit). **robots.txt** parser. **DNS cache**.
- **Extractor:** Parse HTML. Extract links. Normalize. Filter (same domain, external, etc.). **Jsoup**, **BeautifulSoup**.
- **Distributed:** **Kafka** for URL queue. **Workers** pull, crawl, push. **Coordinator** for sharding. **Scrapy** (Python), **Nutch** (Java) for reference.
- **Real tools:** **Googlebot**, **Bingbot**, **Common Crawl** (open dataset), **Scrapy**, **Nutch**.

---

## Let's Walk Through the Diagram

```
WEB CRAWLER ARCHITECTURE
========================

Seed URLs
    |
    v
+-------------------+
|   URL Frontier    |  (Priority queue, per-domain)
|   - Seen? Bloom   |
|   - Pending: Queue|
+-------------------+
    |
    v
+-------------------+
|   URL Fetcher     |  (Respect robots.txt, rate limit)
|   - 1 req/sec     |
|   - per domain    |
+-------------------+
    |
    v
+-------------------+
|   HTML Parser     |  (Extract links, normalize)
+-------------------+
    |
    v
+-------------------+
|   URL Dedup       |  (Already seen? Skip)
+-------------------+
    |
    v
New URLs --> back to Frontier

Storage: Raw HTML (S3, HDFS) for indexing pipeline
```

---

## Real-World Examples (2-3)

**1. Googlebot:** Crawls the web for Google Search. Billions of pages. Distributed. Politeness. Respects robots.txt. Priority: important pages. They've published on their architecture. Scale: exabytes of data.

**2. Common Crawl:** Open web crawl. Publishes data periodically. Petabytes. Used by researchers, startups. Crawler design: distributed, polite, deduplicated. Good reference for open-source approach.

**3. Bingbot:** Microsoft's crawler. Similar. Distributed. Politeness. SEO community cares about crawl budget.

---

## Let's Think Together

**Question:** Crawl depth-first or breadth-first?

**Pause. Think about it...**

**Answer:** **Breadth-first (BFS)** is standard. Depth-first: you go deep into one site. One domain gets hammered. You might get stuck in infinite loops (calendar links: Jan → Feb → Mar → Jan). BFS: you cover broadly. One page from site A, one from site B. Better politeness. Better coverage. **Priority BFS**—within BFS, prioritize important URLs. So: BFS structure, with priority queue. Not pure FIFO. Important pages first. But still BFS-like—don't go deep into one site before touching others.

---

## What Could Go Wrong? (Mini Disaster Story)

A crawler ignored robots.txt. It crawled /admin, /config, /backup. Sites got overwhelmed. Some had sensitive data. One company blocked the crawler's IP. Then the crawler rotated IPs. They blocked the whole ASN. Legal letters. Bad press. The crawler was from a well-meaning startup. They thought "we just want to index the web." They didn't respect robots.txt. They didn't rate limit. They became a DDoS. The fix: respect robots.txt. Always. Rate limit. 1-2 requests per second per domain. Identify yourself (User-Agent). Be polite. Crawling is a privilege. Abuse it, lose it.

---

## Surprising Truth / Fun Fact

**Common Crawl** publishes ~3 billion pages per crawl. They run monthly. The data is free. Researchers use it for NLP, search, analysis. Many startups built their first search index on Common Crawl. You don't need to crawl the web yourself—you can use their data. But building a crawler teaches you: distributed systems, rate limiting, deduplication, scale. It's a classic interview problem for a reason.

---

## Quick Recap (5 bullets)

- **URL frontier:** Priority queue. Per-domain queues. BFS-like. Prevents hammering one site.
- **Robots.txt:** Always respect. Cache. Re-fetch. Legal and ethical.
- **Politeness:** Rate limit per domain. 1-2 req/sec. Spread load.
- **Deduplication:** URL (normalize, Bloom filter). Content (simhash) optional.
- **Distributed:** Shard URLs. Workers. Kafka/SQS. Fault tolerant. Billions of URLs.

---

## One-Liner to Remember

> **URL frontier, per-domain queues, robots.txt, rate limit, deduplicate. BFS with priority. Scale with sharding.**

---

## Next Video

Web crawler done. Next: **proximity service**. "Find restaurants within 2 km." "Nearby gas stations." Geospatial indexing. Low latency. Yelp, Google Maps, Uber. How do you build it? Let's design it.
