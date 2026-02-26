# Design Problem: Ride-Sharing Service

## Video Length: ~4-5 minutes | Level: Senior-Staff

---

## The Hook (20-30 seconds)

You open the app. You see cars nearby. You request a ride. Seconds later, a driver accepts. You watch their dot move toward you. ETA updates. Price is set. How? **Geospatial indexing**. **Real-time location**. **Matching algorithm**. **Surge pricing**. Uber, Lyft, Ola—they run this at massive scale. Let's design it.

---

## The Story

**Requirements.** **Match rider and driver**—find nearby available drivers. Low latency. **Real-time location**—driver's position updates every few seconds. Rider sees it. **ETA**—how long until pickup? Until destination? **Pricing**—base fare, distance, time, surge. **Scale**—millions of riders, millions of drivers. Global.

**Geospatial indexing.** "Find drivers within 2 km of this lat/long." Naive: scan all drivers. O(n). Doesn't scale. You need **spatial indexing**. **Geohash**—encode lat/long into a string. Nearby places share prefix. "Find all in geohash prefix 9q8yy" → fast. **Quadtree**—divide space recursively. Each cell has children. Query: traverse tree, find cells in radius. **R-tree**—bounds for regions. Good for "intersects" queries. **Redis** has **GEO** commands. **PostGIS** (PostgreSQL) has spatial indexes. **Elasticsearch** has geo queries. Pick based on scale and features.

**Matching algorithm.** Rider requests. System finds drivers in radius (e.g., 5 km). Rank by: distance, ETA, rating. Assign? **Greedy**—nearest available. **Batch**—every N seconds, batch match requests to drivers. Better global optimization. **Real-time**—push to nearest driver. They accept or decline. If decline, next driver. Timeout: expand radius or surge. Matching is a **two-sided marketplace**. Balance supply and demand.

**Real-time location updates.** Drivers send location every 4-5 seconds. **WebSocket** or **long polling**. Backend receives. Updates **geospatial index**. Pushes to rider's app (for "driver approaching" view). Scale: millions of WebSocket connections. **Connection management**—sticky sessions, horizontal scaling. **Message fan-out**—rider subscribes to their driver's updates. Only that stream. Don't broadcast all drivers to all riders.

**Surge pricing.** Demand > supply. Prices go up. **Dynamic pricing**—multiplier based on ratio of requests to available drivers. 2x, 3x. Algorithm: real-time demand/supply per zone. Update every few minutes. Controversial but core to the model. Design: pricing service. Input: zone, time, demand, supply. Output: multiplier. Cache at edge. Low latency.

---

## Another Way to See It

A taxi stand. Drivers line up. Riders arrive. Dispatcher matches: "You, go to this address." But there's no physical stand. Drivers are everywhere. Riders are everywhere. The "dispatcher" is an algorithm. It needs a map of where everyone is. Geospatial index is that map. Matching is the dispatch. Real-time updates keep the map current.

---

## Connecting to Software

- **Geospatial indexing:** **Geohash**, **Quadtree**, **R-tree**. **Redis GEO**, **PostGIS**, **Elasticsearch** geo. **S2** (Google's library).
- **Matching:** Find drivers in radius. Rank. Assign or offer. Greedy vs batch. Timeout and retry.
- **Real-time:** **WebSocket** for driver location. **Pub/Sub** (Redis, Kafka) for fan-out to rider. **Sticky sessions** for connection affinity.
- **Pricing:** Base + distance + time. **Surge multiplier** from demand/supply. **Pricing service** with cache.
- **Real tools:** **Uber** (H3 hexagons, custom), **Lyft**, **Ola**. **Mapbox**, **Google Maps** for routing and ETA.

---

## Let's Walk Through the Diagram

```
RIDE-SHARING FLOW
=================

Rider App              Matching Service         Driver App
    |                         |                      |
    |-- Request ride -------->|                      |
    |   (pickup lat/long)     |                      |
    |                         |-- Query geo index -->| (find drivers in 5km)
    |                         |<-- [driver1, driver2]|
    |                         |                      |
    |                         |-- Push to driver1 --->| "Ride request"
    |                         |<-- Accept -----------|
    |<-- "Driver assigned" ---|                      |
    |                         |                      |
    |                         |   WebSocket: driver location every 5s
    |<-- Driver location -----|<---------------------|
    |   (show on map)         |                      |
    |                         |                      |
    |-- Trip complete ------->|                      |
    |                         |-- Update driver ----->| (available again)
```

---

## Real-World Examples (2-3)

**1. Uber:** Uses **H3** (hexagonal hierarchical spatial index). Better than geohash for some queries. They've published on it. Matching: real-time. Surge: dynamic by zone. Scale: millions of trips per day. Real-time location: WebSocket, high frequency.

**2. Lyft:** Similar architecture. Geospatial. Matching. Surge. They've discussed their matching algorithm—batch vs real-time trade-offs. Driver-rider matching is a research problem. Optimization, fairness, ETA.

**3. Ola (India):** Same core. Added: auto-rickshaws, bikes. Multiple vehicle types. Matching considers vehicle type. Geospatial + type filter.

---

## Let's Think Together

**Question:** Driver sends location every 1 second or every 5 seconds?

**Pause. Think about it...**

**Answer:** **4-5 seconds** is typical. 1 second: smoother animation on rider's map. But 5x more data. 5x more server load. 5x more battery drain on driver's phone. For "driver approaching" view, 5 seconds is fine. Rider doesn't need sub-second precision. For **ETA accuracy**, you might want more frequent during last mile. Adaptive: 5s normally, 1s when driver is within 500m. Trade-off: UX vs cost. Most apps: 4-5 seconds. Good enough. Scale matters.

---

## What Could Go Wrong? (Mini Disaster Story)

A ride-sharing startup had a bug in their geospatial index. Drivers in a dense downtown area—hundreds in 1 km². Query "drivers within 2 km" returned 10,000. No limit. Response payload: 10 MB. Slow. App crashed. Mobile networks choked. The fix: **limit results** (top 50 by distance). **Paginate** if needed. **Don't return full driver objects**—return IDs and locations. Fetch details on demand. Lesson: geospatial queries can explode. Always limit. Rank. Return minimal data. Scale the response, not just the query.

---

## Surprising Truth / Fun Fact

Uber's **H3** (hexagonal hierarchical spatial index) has a key property: every hexagon has exactly 7 neighbors (6 sides + center). Geohash cells vary in shape and neighbor count. H3 is more uniform. Better for aggregation—"how many drivers in this zone?" Hexagons tile nicely. Uber open-sourced H3. Used beyond ride-sharing—logistics, gaming, climate. Geospatial indexing has many options. H3 is a modern choice.

---

## Quick Recap (5 bullets)

- **Geospatial indexing:** Geohash, quadtree, H3. Find "nearby" in O(log n). Redis GEO, PostGIS.
- **Matching:** Query drivers in radius. Rank. Push to driver. Accept/decline. Timeout, expand, surge.
- **Real-time location:** WebSocket. Driver sends every 4-5s. Fan-out to rider. Scale connections.
- **Surge pricing:** Demand/supply ratio. Multiplier. Dynamic. Per zone. Pricing service.
- **Scale:** Sticky sessions. Connection management. Limit query results. Minimal payload.

---

## One-Liner to Remember

> **Geospatial index for "nearby." WebSocket for real-time. Match, push, accept. Surge when demand exceeds supply.**

---

## Next Video

Ride-sharing done. Next: **web crawler**. Discover URLs. Download pages. Billions of pages. Politeness. Deduplication. Googlebot, Common Crawl. How do you build it? Let's design it.
