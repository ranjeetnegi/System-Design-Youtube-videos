# The N+1 Query Problem (And How to Fix It)

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

You ask a librarian for a list of 50 book titles. She gives it to you. Then you say: "Now, for book 1, who's the author?" She looks it up. "For book 2, who's the author?" She looks again. Fifty times. You could have asked once: "Give me all 50 books with their authors." One trip. The N+1 problem is when you make 1 query to get a list, then N more queries to get details for each item. It's the most common performance killer in database-backed apps.

---

## The Story

Here's the pattern. **Query 1:** "Get me all users." You get 50 users. **Query 2 through 51:** "Get me the orders for user 1." "Get me the orders for user 2." Fifty more queries. That's **1 + N**—hence **N+1**. You fetched 50 users. For each user, you made another round trip to the database. Fifty users, fifty extra queries. If each query takes 5ms, that's 250ms just for the "detail" queries. Plus the first one. Your page load just became a crawl.

Why does it happen? **ORMs** and **lazy loading**. You write: `users = User.all`. Then in your template: `user.orders`. The ORM doesn't load orders until you access them. First user? Query. Second user? Query. Loop over 50 users? Fifty queries. It's convenient. It's also a trap. Rails, Django, Hibernate, Sequelize—they all do this by default. The code looks clean. The database weeps.

How do you fix it? **Eager loading**: tell the ORM upfront. "When you fetch users, also fetch their orders." One query with a JOIN. Or two queries: one for users, one for orders, then merge in memory. **Batch loading**: fetch orders for user IDs 1–50 in one query. `WHERE user_id IN (1,2,3,...,50)`. **DataLoader pattern**: used in GraphQL. Batch and cache. When you request user 1's orders, DataLoader waits a tick, collects all pending "get orders for user X" requests, then runs one batched query. N+1 becomes 2.

---

## Another Way to See It

You're at a buffet. N+1: you take one plate, go to the table, sit down, eat. Then go back for another plate. Fifty plates, fifty trips. Eager loading: you grab a tray, load all 50 items at once, one trip. Batch loading: you and four friends each get 10 items, one trip per person. Still better than 50 solo trips.

---

## Connecting to Software

- **N+1 pattern:** 1 query for list + N queries for details per item. Caused by **lazy loading** in ORMs.
- **Eager loading:** `User.includes(:orders)` (Rails), `select_related()` / `prefetch_related()` (Django), `JOIN FETCH` (Hibernate). One or two queries instead of N+1.
- **JOIN queries:** Write raw SQL or use ORM joins. `SELECT users.*, orders.* FROM users LEFT JOIN orders ON users.id = orders.user_id`. Single query.
- **Batch loading:** `WHERE user_id IN (...)` — fetch all related data in one query, merge in application code.
- **DataLoader:** GraphQL pattern. Batches and caches. **dataloader** (JavaScript), **aiodataloader** (Python). Eliminates N+1 in GraphQL resolvers.
- **Impact:** 50 users = 51 queries vs 1–2. 50x more round trips. Latency explodes. At scale, databases hit connection limits.

---

## Let's Walk Through the Diagram

```
N+1 (BAD):
  App                    Database
   |                         |
   |-- SELECT * FROM users --|--> 50 rows
   |<-------------------------|
   |                         |
   |-- SELECT orders WHERE user_id=1 ---|--> 3 rows
   |<-------------------------------------|
   |-- SELECT orders WHERE user_id=2 ---|--> 5 rows
   |<-------------------------------------|
   |-- SELECT orders WHERE user_id=3 ---|--> ...
   |   ... 47 more round trips ...
   |
   Total: 1 + 50 = 51 queries

EAGER LOADING (GOOD):
  App                    Database
   |                         |
   |-- SELECT users.*, orders.*          |
   |   FROM users                         |
   |   LEFT JOIN orders ON users.id =     |
   |   orders.user_id -------------------|--> 50 users + 150 orders
   |<-------------------------------------|
   |
   Total: 1 query
```

---

## Real-World Examples (2-3)

**1. Rails:** `User.all` with `user.orders` in a view = N+1. Fix: `User.includes(:orders).all`. Rails runs 2 queries: one for users, one for orders, then merges. Or `User.joins(:orders)` for a single JOIN. The difference between 51 queries and 2 is one line.

**2. Django:** `User.objects.all()` then `user.orders.all()` in a template = N+1. Fix: `User.objects.prefetch_related('orders')` or `select_related()` for single relations. Django's ORM docs explicitly warn about this. It's the first thing they tell you to optimize.

**3. GraphQL + DataLoader:** Every resolver that fetches a related entity can trigger N+1. "Get 100 posts. For each post, get the author." 101 queries. DataLoader: collect all "get author for post X" in one tick, run `SELECT * FROM users WHERE id IN (...)`, cache. One query. Used by GitHub's GraphQL API, Shopify.

---

## Let's Think Together

**Question:** You have a list of 100 products. Each product has a category. You're displaying "Product name, Category name." How many queries does your ORM make if you use lazy loading? And how many with eager loading?

**Pause. Think about it...**

**Answer:** Lazy loading: 1 (products) + 100 (categories) = 101 queries. Eager loading: 1 (products) + 1 (categories for those IDs) = 2 queries. Or one JOIN. 50x fewer round trips. On a page that loads in 200ms, 101 queries could add 500ms+ of database time alone. Eager loading: 10ms.

---

## What Could Go Wrong? (Mini Disaster Story)

A startup launched a "featured sellers" page. Fifty sellers, each with 5 products. Lazy loading. 1 + 50 = 51 queries. Fine in dev. In production, 10,000 concurrent users. 510,000 queries per second. The database connection pool: 100 connections. Exhausted in milliseconds. "Database connection timeout." Page loads: 30 seconds or 502. They thought they needed a bigger database. They needed `includes(:products)`. One line. Two queries. Problem gone.

---

## Surprising Truth / Fun Fact

**Hibernate** in Java has a default batch size of 1 for lazy loading. That means: fetch 100 entities, and each `getRelated()` triggers one query. Set `@BatchSize(size=10)` and Hibernate batches: 10 requests become 1 query. Same data. 10x fewer round trips. A single annotation can fix N+1.

---

## Quick Recap (5 bullets)

- **N+1:** 1 query for list + N queries for details. Caused by lazy loading in ORMs.
- **Eager loading:** `includes`, `prefetch_related`, `JOIN FETCH`—load related data upfront. 1–2 queries.
- **Batch loading:** `WHERE id IN (...)` — one query for all related data, merge in code.
- **DataLoader:** GraphQL pattern. Batch and cache. Eliminates N+1 in resolvers.
- **Impact:** 50x more round trips. Latency, connection exhaustion. Fix it first when optimizing.

---

## One-Liner to Remember

> N+1 is when you ask for a list, then ask for each item's details one by one. Eager load, batch, or JOIN—and ask once.

---

## Next Video

You fixed N+1. But your dashboard query still scans millions of rows every time. What if the database could pre-compute that result? That's a **materialized view**. That's next.
