# Materialized Views: Pre-Computed Query Results

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

You run a report every Monday morning. Same query. Same 10 million rows. Same 5-minute wait. What if you ran it once, saved the result, and just read the saved file? That's a materialized view. The database runs the query, stores the result like a table, and you read from that. Fast. The trade-off? The data is a snapshot. It might be stale. When to refresh—that's the question.

---

## The Story

A **regular view** is a saved query. Every time you `SELECT * FROM my_view`, the database runs the underlying query. Fresh data. But if that query joins five tables and scans millions of rows, it's slow every time. A **materialized view** is different. The query runs once. The result is **stored**—like a table. When you read from it, you're reading pre-computed data. No join. No scan. Just a table lookup. Fast.

When do you refresh? **Manual:** You run `REFRESH MATERIALIZED VIEW my_view` when you want. Good for reports that don't need real-time data. **Periodic:** A cron job refreshes every hour, every night. Dashboards. Analytics. **On change:** Some databases support **incremental refresh**—when the base tables change, the materialized view updates. PostgreSQL has `REFRESH MATERIALIZED VIEW CONCURRENTLY`—refreshes without locking reads. **Trade-off:** Stale data vs fast reads. A dashboard that's 5 minutes old is usually fine. A stock trading system that's 5 minutes old is not.

**Use cases:** Dashboards (daily sales, active users). Reporting (monthly aggregates). Analytics (sum, count, avg over large tables). **Comparison to caching:** A cache stores query results in Redis or Memcached. A materialized view stores them in the database. Both are pre-computed. Cache: application-level, TTL, invalidation logic. Materialized view: database-level, schema, SQL. Use materialized views when the data shape is complex and you want it managed by the database.

---

## Another Way to See It

A regular view is a recipe. Every time you want a cake, you bake it. A materialized view is a cake you baked yesterday and stored in the fridge. Want a slice? Grab it. Fast. But it's not fresh—it's yesterday's cake. Refresh when you bake a new one.

---

## Connecting to Software

- **Regular view:** Saved query. Runs every time you read. Always fresh. Can be slow.
- **Materialized view:** Stored result. Reads are fast. Data is stale until refreshed.
- **PostgreSQL:** `CREATE MATERIALIZED VIEW mv_sales AS SELECT ...` then `REFRESH MATERIALIZED VIEW mv_sales`. Use `CONCURRENTLY` to avoid blocking reads.
- **Oracle, SQL Server, Snowflake:** All support materialized views. Syntax varies. Some have incremental refresh.
- **When to use:** Dashboards, reporting, aggregations over large tables. When the query is expensive and real-time isn't critical.
- **vs Caching:** Cache = application-level, key-value. Materialized view = database-level, SQL, schema. Both pre-compute. Use MV when the result is complex and you want it in the DB.

---

## Let's Walk Through the Diagram

```
REGULAR VIEW:
  SELECT * FROM sales_summary_view
       |
       v
  Database runs: SELECT region, SUM(amount) FROM orders JOIN ...
       |
       v
  Scans millions of rows, joins tables — SLOW every time
       |
       v
  Returns result

MATERIALIZED VIEW:
  CREATE MATERIALIZED VIEW sales_summary AS
    SELECT region, SUM(amount) FROM orders JOIN ... GROUP BY region;
       |
       v
  Database runs query ONCE, stores result in a table
       |
       v
  SELECT * FROM sales_summary_view
       |
       v
  Reads from stored table — FAST (no join, no scan)
       |
       v
  (Data is stale until REFRESH MATERIALIZED VIEW sales_summary)
```

---

## Real-World Examples (2-3)

**1. Analytics dashboard:** "Daily active users by country." Query: `SELECT country, COUNT(DISTINCT user_id) FROM events WHERE date = today GROUP BY country`. That scans millions of rows. Create a materialized view. Refresh at midnight. Dashboard reads from the view. Sub-second. Data is up to 24 hours old. Acceptable for a dashboard.

**2. Reporting:** "Monthly revenue by product." Same idea. Complex JOIN across orders, products, refunds. Materialized view. Refresh every hour or every night. Reports run in milliseconds. Finance gets the numbers. No 10-minute query.

**3. PostgreSQL in production:** `REFRESH MATERIALIZED VIEW CONCURRENTLY` lets you refresh without locking. Other queries can still read the old view. When refresh completes, the new data is visible. Critical for 24/7 systems. Without CONCURRENTLY, reads block during refresh.

---

## Let's Think Together

**Question:** You have a dashboard showing "Total sales this month." It runs a query that takes 30 seconds. You could cache it or use a materialized view. What's the difference?

**Pause. Think about it...**

**Answer:** **Cache:** Store the result in Redis. TTL of 5 minutes. Application checks cache first. If miss, run query, store result. **Materialized view:** Store the result in the database. Refresh on a schedule (e.g., every 5 minutes). Application just reads from the view. Both give you fast reads. Cache: application-managed, flexible TTL, cache invalidation. Materialized view: database-managed, SQL, can be queried with JOINs, filters. Use cache for simple key-value. Use materialized view when the result is a table you want to query further.

---

## What Could Go Wrong? (Mini Disaster Story)

A team created a materialized view for a critical dashboard. Refreshed it nightly. One Monday, a bug in the ETL job corrupted the base tables. The materialized view refreshed. It now contained wrong data. The dashboard showed inflated sales numbers. Decisions were made. Budgets allocated. A week later, someone noticed. The fix: restore from backup. But the real lesson: **test your refresh**. A materialized view is only as good as its source. And consider: what if the refresh fails? Add monitoring. Alert if refresh fails. A stale view is bad. A wrong view is worse.

---

## Surprising Truth / Fun Fact

**Snowflake** and **BigQuery** use materialized views differently—they can automatically optimize queries to use them. You create a materialized view. The query optimizer decides: "This query can be satisfied by the materialized view." It rewrites automatically. You don't have to change your application. The database just gets faster.

---

## Quick Recap (5 bullets)

- **Regular view:** Query runs every time. Fresh. Can be slow.
- **Materialized view:** Result stored. Reads are fast. Stale until refreshed.
- **Refresh:** Manual, periodic (cron), or on change. Trade-off: freshness vs performance.
- **Use cases:** Dashboards, reporting, aggregations. When expensive queries + non-real-time is OK.
- **PostgreSQL:** `REFRESH MATERIALIZED VIEW CONCURRENTLY` to avoid blocking reads.

---

## One-Liner to Remember

> A materialized view is a cake you baked yesterday. Fast to serve. Stale until you bake again. Refresh when you need fresh.

---

## Next Video

Your database is fast. Your queries are optimized. But what happens when the hard drive dies? How do you recover? That's **database backup and recovery**. That's next.
