# Geospatial Indexing: Finding Nearby Things Fast

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

You're in a new city. "Coffee shops near me." Your phone doesn't scan every coffee shop on Earth. It knows you're in a grid cell. It only checks that cell—and maybe the neighbors. That's geospatial indexing. Without it? A database with millions of lat/long rows. "Find within 2 miles." Full table scan. Minutes. With it? Milliseconds. The world is a grid. Index it.

---

## The Story

**Latitude and longitude** are just numbers. (-37.8, 144.9). Put them in a table. "Find restaurants within 2 km of this point." Without an index, the database scans every row. Computes distance. Filters. At 10 million rows, that's a full table scan. Slow. Unacceptable.

**Geospatial indexing** solves this. The idea: **encode location into a structure that groups nearby points**. Three main approaches. **Geohash:** Convert lat/long to a string. "9q8yy" is a cell. "9q8yy0" is a smaller cell inside it. **Prefix = nearby**. Shorter string = larger area. Longer string = more precision. Query "points in 9q8yy" → only that cell. Fast.

**Quadtree:** Recursively divide the 2D plane into four quadrants. Each quadrant divides again. A point belongs to one leaf. Nearby points share ancestor nodes. Query: find the cell containing the point, check neighbors. **R-tree:** Bounding rectangles. Group points into minimum bounding rectangles (MBRs). Trees of rectangles. "Which MBRs overlap my query circle?" Traverse the tree. Skip whole branches.

**PostGIS** (PostgreSQL), **MongoDB 2dsphere**, **Redis GEO**, **Elasticsearch**—they all support geospatial. **Uber** (driver matching), **Yelp** (nearby businesses), **Google Maps**, **DoorDash** (restaurant discovery)—all use it. "Near me" is not a luxury. It's table stakes.

---

## Another Way to See It

A mall directory. "You are here." The directory doesn't list every store with coordinates. It shows a map. Sections: North wing, South wing. Each wing has zones. You're in North, Zone B. "Coffee shops" — you only look at Zone B and maybe Zone A. You don't walk the entire mall. The directory is the index. Your location encodes to a zone. Query is scoped. Geospatial indexing is the same: encode location, scope the search.

---

## Connecting to Software

- **Geohash:** String encoding. "9q8yy" = San Francisco area. Add characters = smaller cell. Prefix match = nearby. Simple. But **boundary issues**—points on opposite sides of a cell boundary can have different hashes despite being close.
- **Quadtree:** Recursive 2D subdivision. NW, NE, SW, SE. Points in same leaf = close. Good for in-memory. Used in game engines, graphics.
- **R-tree:** Bounding rectangles. Hierarchical. Overlap queries. **PostGIS** uses it. Industry standard for GIS.
- **PostGIS:** PostgreSQL extension. Full GIS. R-tree under the hood. Used by mapping apps, logistics.
- **MongoDB 2dsphere:** GeoJSON. Spherical geometry. "Near" queries. Good for global apps.
- **Redis GEO:** Geohash-based. GEOADD, GEORADIUS. Sub-millisecond. Proximity, leaderboards.
- **Elasticsearch:** geo_point type. Geohash grid aggregation. Search + geo in one engine.

---

## Let's Walk Through the Diagram

```
GEOHASH: "9q8yy" (San Francisco)
================================

  Level 1: "9"     → Broad region (N. America west)
  Level 2: "9q"    → California
  Level 3: "9q8"   → Bay Area  
  Level 4: "9q8y"  → San Francisco
  Level 5: "9q8yy" → Downtown SF

  Prefix = nearby:
  "9q8yy" and "9q8yz" → Same 4-char prefix "9q8y" → NEARBY
  "9q8yy" and "9q8yn" → Same 4-char prefix → NEARBY
  "9q8yy" and "9q9zz" → Different prefix → FAR

  Query: "Points in 9q8yy" → Only fetch that cell. Fast.

QUADTREE (conceptual)
=====================
        [Root]
       /  |  |  \
    [NW] [NE] [SW] [SE]
      |    |
   [..] [..]  ... (recursive)

  Point in NE quadrant → Check NE and neighbors. Skip NW, SW, SE.
```

---

## Real-World Examples (2-3)

**1. Uber:** "Find drivers within 5 minutes." Rider's location → geohash cell. Query drivers in that cell + adjacent cells. Redis GEO or custom index. Millions of concurrent requests. Latency budget: milliseconds. Geospatial indexing is the backbone.

**2. Yelp:** "Restaurants near me." User's lat/long. MongoDB 2dsphere or Elasticsearch. Index on location. $geoWithin or geo_distance query. Results in under 100ms. Without indexing: full scan of millions of businesses. Impossible.

**3. DoorDash / food delivery:** "Restaurants that deliver to my address." Delivery zone = polygon. Restaurant location = point. "Is point in polygon?" PostGIS. "Restaurants within 3 miles?" Geohash or R-tree. Same idea. Scale.

---

## Let's Think Together

**Question:** You're building a ride-sharing app. 100,000 drivers. 10,000 concurrent ride requests. Each request: "Find nearest available driver." Naive approach: for each request, compute distance to all 100,000 drivers. Will that work?

**Pause. Think about it...**

**Answer:** **No.** 10,000 requests × 100,000 distance calculations = 1 billion operations per second. Not feasible. **Geospatial index:** Rider's location → geohash. Drivers indexed by geohash. Query: drivers in same cell + adjacent cells. Maybe 100 drivers to check. 10,000 × 100 = 1 million. Doable. Redis GEO, or a custom grid. Index. Always index.

---

## What Could Go Wrong? (Mini Disaster Story)

A team used geohash for "stores within 10 miles." Precision: 5 characters. Worked in most cases. Then they hit a **boundary**. Two stores, 0.5 miles apart. One just inside geohash "9q8yy." The other just outside—"9q8yz." Different cells. A user between them: "Stores near me" returned only one. The other—equally close—was in a "neighbor" cell they didn't query. Users complained. "The store is right there!" The fix: **always query the cell plus all 8 neighbors**. Geohash boundaries are arbitrary. Adjacent cells might contain nearby points. Or use **R-tree** / **2dsphere**—they handle boundaries correctly. The lesson: geohash is simple but has edge cases. Know the boundaries.

---

## Surprising Truth / Fun Fact

**Geohash** was created by Gustavo Niemeyer in 2008. One encoding, now used by Redis, MongoDB, Elasticsearch, and countless apps. The same idea—turn the Earth into a grid of strings—powers "near me" for billions of users. A simple encoding. Global impact.

---

## Quick Recap (5 bullets)

- **Geospatial indexing:** Encode location so nearby points group together. Avoid full table scans.
- **Geohash:** String encoding. Prefix = nearby. Simple. Watch boundary edge cases.
- **Quadtree:** Recursive 2D split. Good for in-memory. Games, graphics.
- **R-tree:** Bounding rectangles. PostGIS standard. Handles polygons, complex queries.
- **Tools:** PostGIS, MongoDB 2dsphere, Redis GEO, Elasticsearch. Uber, Yelp, DoorDash—all use it.

---

## One-Liner to Remember

> **Geospatial indexing: turn "where" into a lookup. Geohash, quadtree, R-tree—nearby points, fast queries. "Near me" at scale.**

---

## Next Video

That wraps up the deep dives. You now have the complete toolkit—from beginner concepts to staff-level design problems. LSM trees, Bloom filters, geospatial indexing. The fundamentals. The trade-offs. The disaster stories. Go build. Go design. Go ship.
