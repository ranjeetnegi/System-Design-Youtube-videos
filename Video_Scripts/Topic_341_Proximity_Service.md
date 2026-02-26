# Design Problem: Proximity Service

## Video Length: ~4-5 minutes | Level: Senior-Staff

---

## The Hook (20-30 seconds)

"Restaurants near me." "Gas stations within 2 miles." "Nearest hospital." You've used it. Yelp, Google Maps, Uber. Type a location. Get a list. Sorted by distance. Sub-second. Millions of places. How? **Geospatial indexing**. **Radius queries**. **Low latency**. It's a different problem from ride-sharing—here you're searching a static (mostly) database of places. Let's design it.

---

## The Story

**Requirements.** **Find nearby places**—within radius (e.g., 2 km). Or "top K nearest." **Low latency**—under 100ms. User is waiting. **Scale**—millions of places. Global. **Ranked**—by distance, or by relevance (rating, popularity). **Filtering**—restaurants only. Open now. Price range. **Real-time updates**—new places added. Hours change. Not fully static.

**Geospatial indexing.** "Find all points within 2 km of (lat, long)." Naive: compute distance to every point. O(n). Millions of points? Too slow. You need **spatial indexing**. **Geohash**—encode lat/long to string. "9q8yy" covers San Francisco. Longer prefix = smaller area. Query: get geohash of center, expand to cover radius, fetch points in those geohashes. **Quadtree**—divide space. Each node has 4 children. Query: traverse, find cells intersecting radius. **R-tree**—bounding boxes. Good for "intersects" and "contains." **PostGIS** (PostgreSQL) has `ST_DWithin`, `ST_Distance`. **Elasticsearch** has `geo_distance`. **Redis GEO** has `GEORADIUS`. **MongoDB** has `$geoWithin`. Pick based on scale and features.

**Database choice.** **PostGIS**—powerful. SQL. Complex queries. Filter + sort + radius. Good for moderate scale. **Elasticsearch**—full-text + geo. "Italian restaurants near me" = text + geo. Scale: millions. **Redis GEO**—in-memory. Fast. Simple radius. Limited filtering. **MongoDB**—document store + geo. Flexible schema. Each has trade-offs. For "places with filters" (type, open, price), PostGIS or Elasticsearch. For "just nearby, fast," Redis GEO.

**Caching popular queries.** "Restaurants near Times Square"—thousands of queries per hour. **Cache** the result. Key: (lat, long, radius, filters). Value: list of places. TTL: 5-10 minutes. Places don't change every second. **Edge caching**—CDN or edge DB. User in NYC gets cached result from NYC edge. Latency: single-digit ms for cache hits. 80% of queries might be cacheable (popular areas).

**Ranking.** Distance is primary. Closer = better. But also: **rating**, **popularity**, **open now**. Multi-factor ranking. **Elasticsearch** function_score. **PostGIS** + custom ORDER BY. Or: fetch by radius, re-rank in application. Trade-off: DB does it (simpler) vs app does it (flexible). For "top 20 nearest restaurants by rating," DB can do it. For complex ML ranking, app re-ranks.

---

## Another Way to See It

A phone book. But instead of alphabetical, it's organized by location. "Everything in downtown" is one section. "Everything in midtown" is another. You're in downtown. You open that section. You get nearby places. Geospatial index is that organization—by location, not by name. Query "near me" = "open my section." Fast.

---

## Connecting to Software

- **Geospatial indexing:** **Geohash**, **Quadtree**, **R-tree**. **PostGIS**, **Elasticsearch** geo, **Redis GEO**, **MongoDB** 2dsphere.
- **Queries:** `ST_DWithin` (PostGIS), `geo_distance` (Elasticsearch), `GEORADIUS` (Redis). Radius or top-K.
- **Caching:** **Redis** for popular (lat, long, radius) queries. TTL 5-10 min. **CDN** for API responses at edge.
- **Filtering:** Type, open hours, price. **Elasticsearch** filters. **PostGIS** WHERE clause. Index appropriately.
- **Real tools:** **Yelp** (places), **Google Maps** (Places API), **Uber** (nearby drivers), **Foursquare** (venues).

---

## Let's Walk Through the Diagram

```
PROXIMITY SERVICE
=================

User: "Restaurants within 2km"
         |
         v
+------------------+
|  Cache (Redis)   |  Key: (lat, long, 2km, restaurants)
|  Hit? Return     |  Miss? Query DB
+------------------+
         |
         v (cache miss)
+------------------+
|  PostGIS/ES      |  SELECT * FROM places
|  ST_DWithin(     |  WHERE type='restaurant'
|    point, radius |  AND ST_DWithin(geom, point, 2km)
|  )               |  ORDER BY distance
|  LIMIT 20        |  LIMIT 20
+------------------+
         |
         v
+------------------+
|  Re-rank (opt)   |  By rating, open now, etc.
+------------------+
         |
         v
    Return to user
```

---

## Real-World Examples (2-3)

**1. Yelp:** "Restaurants near me." Millions of businesses. Geospatial + filters (price, rating, open). They've discussed their search infrastructure. Elasticsearch or similar. Caching for popular areas. Low latency.

**2. Google Maps:** Places API. "Nearby" is core. Scale: global. They use proprietary infrastructure. Geospatial at scale. Ranking: distance + relevance + popularity. The gold standard.

**3. Uber:** "Nearby drivers" is a proximity query. Same problem. Different data (drivers vs places). Geospatial index. Real-time updates. Redis GEO or custom. Same principles.

---

## Let's Think Together

**Question:** Cache key: exact (lat, long) or rounded (e.g., 3 decimal places)?

**Pause. Think about it...**

**Answer:** **Rounded.** Exact (lat, long) means almost every query is unique. 37.7749 vs 37.7750—different keys. Cache hit rate: near zero. Round to 3-4 decimal places. 37.775 covers ~10-100m. Queries within that area get same cache key. Hit rate soars. Trade-off: slight imprecision. User at 37.7749 and 37.7751 get same cached result. For "restaurants within 2 km," that's fine. The result set is nearly identical. Round. Cache. Win.

---

## What Could Go Wrong? (Mini Disaster Story)

A proximity service used a database without spatial index. "Find restaurants within 2 km" ran as: fetch all restaurants, compute distance in application, filter, sort. 10 million restaurants. Every query: full scan. 30 seconds. Users left. The fix: they added **PostGIS** with spatial index. Query time: 50ms. Same data. Right index. Lesson: geospatial queries without spatial index don't scale. Always use a proper geo index. PostGIS, Elasticsearch, Redis GEO—pick one. Don't scan.

---

## Surprising Truth / Fun Fact

**Geohash** has a quirk: two points can be close in space but have very different geohashes. Boundary problem. Points on opposite sides of a cell boundary. **Quadtree** has similar issues. Solutions: **query multiple cells**—expand the geohash, get neighbors. Or use **H3** (hexagons)—more uniform. For "within 2 km," you typically expand the search area slightly to cover boundary cases. Don't assume one geohash cell is enough. Get the cell + neighbors. Ensures you don't miss edge points.

---

## Quick Recap (5 bullets)

- **Geospatial indexing:** Geohash, quadtree, R-tree. PostGIS, Elasticsearch, Redis GEO. Don't full scan.
- **Radius query:** ST_DWithin, GEORADIUS. "Points within X km." Index makes it O(log n).
- **Caching:** Popular (lat, long) rounded. TTL 5-10 min. 80% cache hit possible.
- **Filtering:** Type, open, price. Combine with geo. Index for both.
- **Ranking:** Distance first. Then rating, popularity. DB or application.

---

## One-Liner to Remember

> **Geospatial index for "nearby." Cache popular queries. Round coordinates for cache key. Never full scan.**

---

## Next Video

Proximity service done. You've designed ID generators, autocomplete, booking, file sync, video streaming, ride-sharing, crawlers, and proximity. Next, we go deeper into the **data structures** that power these systems. First up: **LSM Tree**—the secret behind many high-write databases. LevelDB, RocksDB, Cassandra. How it works. Why it's fast. Let's dive in.
