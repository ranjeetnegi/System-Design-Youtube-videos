# What Are Sticky Sessions?

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

You're at a restaurant. You order appetizers. The waiter remembers your table. Your main course arrives—same waiter. Dessert—same waiter. Now imagine a different restaurant. Appetizers: Waiter A. Main course: Waiter B. "I had the steak." Waiter B has no idea. Your order is lost. Sticky sessions are Waiter A. Same customer, same server. Your "state" stays put.

---

## The Story

You have five servers behind a load balancer. Round-robin. User 1 hits Server 1. User 2 hits Server 2. Fair. But User 1 logs in. Server 1 stores their session in memory—"User 1 is logged in, cart has 3 items." User 1's next request? Round-robin sends it to Server 3. Server 3 has no session. "Who are you? Empty cart." User 1 is confused. Logged out. Cart gone. The load balancer did its job—distributed fairly. But the application needed **consistency**. Same user, same server.

**Sticky sessions**—also called **session affinity**—solve that. The load balancer remembers: "This user goes to Server 2." How? Two common ways. **IP hash:** Hash the client IP. Same IP always maps to the same server. No cookies needed. **Cookie-based:** The load balancer sets a cookie on the first response—"SERVER=2". Next request, the client sends the cookie. Load balancer reads it, routes to Server 2. Same user, same server. Session lives. Cart persists.

The trade-off? **Uneven load.** If 1000 users with the same IP (corporate NAT) share one connection, they all hit Server 2. Server 2 drowns. Others idle. Server 2 dies? All those users lose their sessions. **Failover is harder.** Stateless is better. Store session in **Redis**. Any server can serve any user. No stickiness needed. But when you can't go stateless—legacy apps, in-memory session—sticky sessions are the band-aid.

---

## Another Way to See It

A library. Without stickiness: You return a book. Clerk A puts it on the cart. You come back for another book. Clerk B helps you. "Where's the book I just returned?" Clerk B doesn't know. With stickiness: You always get Clerk A. Same clerk, same cart, same context. Your "state" is in one place.

---

## Connecting to Software

- **Session affinity:** Same client → same server. Needed when **session state** lives in server memory (e.g., in-memory session, shopping cart).
- **IP hash:** `hash(client_ip) % server_count`. Same IP → same server. Pros: No cookies. Works with HTTP and non-HTTP. Cons: Corporate NAT = many users, one IP = one server overloaded. IP can change (mobile).
- **Cookie-based:** LB sets cookie (e.g., `SERVERID=abc123`). Client sends it back. LB routes to that server. Pros: Works across NAT. Cons: Client must accept cookies. Cookie can be lost (cleared, incognito).
- **When to avoid:** Prefer **stateless** design. Store session in **Redis**, **Memcached**, or database. Any server can serve any request. No stickiness. Scalable. Resilient.
- **Real tools:** **AWS ALB** supports sticky sessions via `stickiness` policy. **AWS ELB** (classic) has `stickiness` with configurable duration. **NGINX** uses `ip_hash` or `hash $cookie_xxx`. **HAProxy** uses `stick-table` or `cookie` directive.

---

## Let's Walk Through the Diagram

```
WITHOUT STICKY SESSIONS              WITH STICKY SESSIONS
========================             ====================

Request 1 (User A) → LB → Server 1   Request 1 (User A) → LB → Server 1
  (Session stored on S1)               (Session stored on S1)
                                       LB sets cookie: SERVER=1

Request 2 (User A) → LB → Server 3   Request 2 (User A) → LB → Server 1
  (S3 has no session! Lost!)            (Cookie says S1. Same server. Session OK.)


IP HASH:  hash(192.168.1.5) % 3 = 1  →  Always Server 1
COOKIE:   First response: Set-Cookie: SERVERID=1
          Next request: Cookie: SERVERID=1  →  Route to Server 1
```

---

## Real-World Examples (2-3)

**1. E-commerce checkout:** Legacy system. Shopping cart in server memory. User adds items on Server 2. Without stickiness, next request might hit Server 4. Cart gone. Sticky sessions keep the user on Server 2. Band-aid until they move cart to Redis.

**2. AWS ALB:** Enable "sticky sessions" with a duration (e.g., 1 day). ALB generates a cookie. Same cookie = same target. Used when migrating from stateful to stateless—temporary fix.

**3. WebSocket connections:** Long-lived. State on the server. Sticky sessions ensure the same client reconnects to the same server. Or use IP hash. Without it, reconnection might land on a different server—connection state lost.

---

## Let's Think Together

**Question:** Your app stores user sessions in Redis. Do you need sticky sessions?

**Pause. Think about it...**

**Answer:** **No.** If session state is in Redis, any server can serve any request. Server 1, 2, 3—they all read from the same Redis. No need to pin a user to one server. Sticky sessions are for when state lives *in the server*. In-memory session, in-memory cart. Once you externalize state (Redis, DB), you're stateless. Sticky sessions become unnecessary. Better: go stateless. Avoid the stickiness complexity and uneven load.

---

## What Could Go Wrong? (Mini Disaster Story)

A company used IP hash for sticky sessions. Worked fine. Then a big enterprise customer—50,000 employees—all behind one corporate NAT. One IP. IP hash sent all 50,000 to Server 3. Server 3 melted. CPU 100%. Timeouts. "Your site is down." Servers 1, 2, 4, 5 were idle. One IP, one server, one point of failure. The fix? Switch to cookie-based stickiness. Traffic spread across IPs. Or better: move sessions to Redis. Go stateless. IP hash + corporate NAT = disaster waiting to happen.

---

## Surprising Truth / Fun Fact

**Google** and **Netflix** design for statelessness. They avoid sticky sessions whenever possible. Session in distributed cache. Any server, any request. Sticky sessions are a last resort for legacy systems. The goal: no server "owns" a user. Any server can serve anyone. That's true scale.

---

## Quick Recap (5 bullets)

- **Sticky sessions:** Same client → same server. For stateful apps (session in memory).
- **IP hash:** Same IP → same server. Simple. Breaks with corporate NAT (many users, one IP).
- **Cookie-based:** LB sets cookie. Client sends it. Route by cookie. Works across NAT.
- **Trade-off:** Uneven load. Failover loses sessions. Prefer stateless (Redis, DB).
- **When to avoid:** If you can store session externally, do it. No stickiness needed.

---

## One-Liner to Remember

> **Sticky sessions pin a user to a server. Use them when you must. Avoid them when you can. Stateless is better.**

---

## Next Video

You've got load balancers. Sticky sessions. But how do users even *find* your servers? They type a domain name. **DNS**—the phonebook of the internet. How does DNS work? What happens when you type google.com? Next: DNS and how it works.
