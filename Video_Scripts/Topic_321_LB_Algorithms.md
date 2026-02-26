# What Are Load Balancing Algorithms?

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

Five cashiers. One host. The host could just point: "You, counter 1. You, counter 2." Take turns. Fair. But what if cashier 3 is twice as fast? What if one customer has a cart full of items and another just needs milk? The host has choices. Round-robin. Send the big order to the fast cashier. Send the quick one anywhere. That host isn't just distributing—they're deciding *how* to distribute. That decision? The load balancing algorithm.

---

## The Story

You have a load balancer. It sits in front of five servers. Traffic arrives. The balancer must pick: which server gets this request? The naive answer: "Just take turns." Request 1 → Server 1. Request 2 → Server 2. Request 3 → Server 3. That's **round-robin**. Simple. Predictable. But here's the catch: not all requests are equal. Some are quick API pings. Some are long-running video uploads. If you blindly take turns, you might send five long uploads to five different servers—or accidentally pile three onto one. Round-robin assumes every request costs the same. In the real world, that's rarely true.

**Weighted round-robin** fixes part of that. Server 1 has 32 cores. Server 2 has 8. Give Server 1 four times the traffic. You assign weights: 4, 1, 1, 1, 1. Now the big server does more work. But you still don't know which requests are heavy. A better approach: **least connections**. Send each new request to the server with the fewest active connections. The one that's least busy *right now*. If Server 3 is handling 2 connections and Server 1 has 50, the next request goes to Server 3. Dynamic. Adaptive.

**IP hash** is different. Hash the client's IP. Same IP always maps to the same server. Why? **Session affinity**. Maybe that user's session lives in memory on Server 2. You need them to keep hitting Server 2. IP hash guarantees it—as long as the server count doesn't change. **Random** is the simplest: pick a server at random. Statistically even over time. No state. No memory. Good when you don't care about affinity and want minimal logic.

---

## Another Way to See It

Think of a gym with five treadmills. Round-robin: "Next person, treadmill 1. Next, treadmill 2." Everyone gets a turn. Weighted: Treadmill 1 is a commercial beast—it gets two people for every one person on the others. Least connections: The treadmill with nobody on it gets the next person. IP hash: "You've been here before—you always use treadmill 3." Random: Spin a wheel. Each algorithm is a different philosophy of fairness.

---

## Connecting to Software

- **Round Robin:** Request 1→S1, 2→S2, 3→S3, 4→S4, 5→S5, 6→S1. Use when requests are roughly equal. **NGINX**, **HAProxy**, **AWS ALB** all support it.
- **Weighted Round Robin:** Assign weights (e.g., 3:1:1). Bigger servers get more traffic. Use when server capacity varies.
- **Least Connections:** Route to the server with fewest active connections. Ideal for **long-lived connections** (WebSockets, streaming, uploads). HAProxy calls it `leastconn`.
- **IP Hash:** `hash(client_ip) % server_count`. Same client → same server. Use for **session affinity** when you can't go stateless.
- **Random:** Pick a server randomly. Simple. No coordination. Use when you want even distribution and don't need affinity.

**NGINX** uses `least_conn` for connection-based balancing. **HAProxy** offers `roundrobin`, `leastconn`, `source` (IP hash). **AWS ALB** supports round-robin and least outstanding requests (similar to least connections). The algorithm is configurable—choose based on your workload.

---

## Let's Walk Through the Diagram

```
                    INCOMING REQUESTS
                           |
                           v
                  +------------------+
                  |  LOAD BALANCER   |
                  |  (Algorithm)     |
                  +--------+---------+
                           |
        +------------------+------------------+
        |         |         |         |        |
        v         v         v         v        v
    Server 1  Server 2  Server 3  Server 4  Server 5
    (weight 4) (w:1)    (w:1)     (w:1)     (w:1)

ROUND ROBIN:     R1→S1, R2→S2, R3→S3, R4→S4, R5→S5, R6→S1...
LEAST CONN:      R→server with fewest active connections
IP HASH:         hash(192.168.1.5) % 5 = 2 → always Server 2
WEIGHTED:        S1 gets 4x traffic of others
```

---

## Real-World Examples (2-3)

**1. NGINX:** Powers millions of sites. Default `round_robin`. For WebSockets or streaming, switch to `least_conn`. One line in config: `least_conn;`. Same infrastructure, different algorithm, better fit.

**2. AWS ALB:** Uses round-robin by default. For applications with varying request duration, enable "least outstanding requests"—sends to the target with fewest in-flight requests. Same idea as least connections, different name.

**3. HAProxy:** The Swiss Army knife. `balance roundrobin`, `balance leastconn`, `balance source` (IP hash). Used by GitHub, Reddit, Stack Overflow. When they need session affinity for stateful backends, they use `source`. When they need fairness for API traffic, `leastconn`.

---

## Let's Think Together

**Question:** You're running a video streaming service. Users connect, watch for 30 minutes, disconnect. You have 10 servers. Which algorithm do you pick?

**Pause. Think about it...**

**Answer:** **Least connections.** Round-robin would blindly send user 1 to S1, user 2 to S2, etc. But users don't leave at the same time. Some servers would accumulate many long-lived streams. Others would sit idle. Least connections keeps the load even. Each new viewer goes to the server with the fewest active streams. Dynamic. Adaptive. Right fit.

---

## What Could Go Wrong? (Mini Disaster Story)

A startup used round-robin for their file upload API. Seemed fine. Then a customer uploaded 50GB. Round-robin had sent them to Server 3. Server 3's disk filled. CPU spiked. That one upload monopolized the server. Meanwhile, Servers 1, 2, 4, 5 were idle. New requests kept getting round-robin'd to Server 3. Timeouts. "Upload failed." Support tickets. The fix? Switch to **least connections**. Heavy uploads spread across servers. No single server drowned. The algorithm wasn't wrong—it was wrong *for the workload*. Know your traffic. Choose accordingly.

---

## Surprising Truth / Fun Fact

**Google's Maglev** load balancer doesn't use traditional round-robin. It uses **consistent hashing** with minimal disruption—when you add or remove a server, only 1/N of connections need to move. At Google's scale, that matters. A naive round-robin would reshuffle everything on every topology change.

---

## Quick Recap (5 bullets)

- **Round Robin:** Take turns. Simple. Use when requests are similar.
- **Weighted Round Robin:** Bigger servers get more traffic. Use when capacity varies.
- **Least Connections:** Send to the least busy server. Use for long-lived or variable-length requests.
- **IP Hash:** Same client → same server. Use for session affinity.
- **Random:** Pick randomly. Statistically even. Minimal logic.

---

## One-Liner to Remember

> **The algorithm is the host's rulebook. Round-robin takes turns. Least connections sends to the emptiest line. IP hash remembers where you sat. Pick the rule that fits your traffic.**

---

## Next Video

You've picked an algorithm. But *where* does the load balancer look? At IP and port? Or at the full HTTP request—URL, headers, cookies? **Layer 4 vs Layer 7**—the difference between "which port" and "which path." Next: L4 vs L7 load balancing.
