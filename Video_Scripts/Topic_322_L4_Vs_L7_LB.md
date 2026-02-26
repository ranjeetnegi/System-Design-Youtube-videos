# L4 vs L7 Load Balancing

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

A mail sorter. Two kinds. One looks only at the envelope: ZIP code, city. Fast. Doesn't open the letter. The other opens every envelope, reads the first line, checks for keywords. Slower. But it can route "Complaint" letters to the complaints department and "Order" letters to fulfillment. Same mail. Different depth of inspection. That's Layer 4 vs Layer 7. One looks at the outside. One looks inside.

---

## The Story

Your load balancer receives a request. What does it actually *see*? It depends on the layer. **Layer 4**—the transport layer—sees **IP addresses** and **port numbers**. That's it. Source IP, destination IP, source port, destination port. TCP or UDP. The balancer doesn't open the packet. It doesn't know if this is a request for `/api/users` or `/images/logo.png`. It just sees: "Connection from 192.168.1.5 to port 443." Fast. Cheap. Minimal CPU. Forward and done.

**Layer 7**—the application layer—sees the full **HTTP request**. URL path. Headers. Cookies. Method (GET, POST). The balancer can read "Host: api.example.com" and route to the API servers. It can read "Path: /videos" and send to the video CDN. It can terminate **SSL**—decrypt the traffic, inspect it, then re-encrypt to the backend. Smart routing. Path-based. Header-based. Cookie-based. But it costs more. The balancer must parse HTTP. Decrypt TLS. More CPU. More latency. Trade-off: intelligence vs speed.

When do you use which? **L4** when you need raw throughput. Gaming. VoIP. Streaming. Millions of connections. You don't care about the content—just spread the load. **L7** when you need to route by URL, host, or header. Microservices. "Send /api to these servers, /admin to those." **SSL termination** at the balancer so backends don't do crypto. **AWS NLB** is L4. **AWS ALB** is L7. Different tools for different jobs.

---

## Another Way to See It

A highway toll booth. L4: The booth looks at your license plate and car type. "Truck? Lane 1. Car? Lane 2." Fast. L7: The booth asks, "Where are you going?" You say "Airport." "Airport traffic—exit 14." It reads your destination. Smarter routing. Slower. More questions. Same road. Different depth of inspection.

---

## Connecting to Software

- **Layer 4 (L4):** Transport layer. **TCP/UDP**. Sees only IP + port. No content inspection. **AWS NLB**, **HAProxy** in TCP mode, **F5** L4 virtual servers. Use for: high throughput, low latency, streaming, gaming, when you don't need routing by path.
- **Layer 7 (L7):** Application layer. **HTTP/HTTPS**. Sees URL, headers, cookies, body. Can route by path (`/api` vs `/static`), host (`api.example.com` vs `www.example.com`), header. **SSL termination**—decrypt at load balancer, backends get plain HTTP. **AWS ALB**, **NGINX** as reverse proxy, **HAProxy** in HTTP mode. Use for: microservices, path-based routing, host-based routing, header-based routing.
- **HAProxy modes:** `mode tcp` = L4. `mode http` = L7. Same tool, different config.
- **Trade-off:** L4 = faster, simpler, fewer features. L7 = more overhead, more intelligent routing.

---

## Let's Walk Through the Diagram

```
LAYER 4 (Transport)                    LAYER 7 (Application)
===================                    =====================

  Client Request                          Client Request
       |                                       |
       v                                       v
  +--------+                              +--------+
  |  L4 LB |  Sees: IP, Port               |  L7 LB |  Sees: IP, Port, URL,
  |        |  (192.168.1.5)               |        |  Headers, Cookies,
  |        |  (port 443)                  |        |  Host, Method
  +---+----+                              +---+----+
      |                                       |
      |  No content inspection                 |  "Path is /api? → Server 1"
      |  Just forward to any server            |  "Path is /images? → Server 2"
      |                                       |  SSL termination here
      v                                       v
  S1   S2   S3   S4   S5                 S1(api)  S2(admin)  S3(static)
```

---

## Real-World Examples (2-3)

**1. AWS NLB vs ALB:** **NLB** (Network Load Balancer) is L4. Millions of connections per second. Fixed latency. Used for gaming, IoT, streaming. **ALB** (Application Load Balancer) is L7. Path-based routing. Host-based routing. SSL termination. Used for web apps, microservices, APIs. Same AWS account, different tools for different layers.

**2. HAProxy:** One config. `mode tcp` for L4—raw TCP forwarding. `mode http` for L7—inspect URLs, set `acl path_api path_beg /api` and route accordingly. Netflix, GitHub, Reddit use HAProxy. They pick the mode based on the service.

**3. NGINX:** Default is L7. It parses HTTP. You can do `location /api { proxy_pass http://api_backend; }` and `location /images { proxy_pass http://cdn_backend; }`. For raw TCP (e.g., database connections, Redis), use `stream` block—that's L4. Same binary, different modules.

---

## Let's Think Together

**Question:** You're building a real-time multiplayer game. 100,000 concurrent connections. Low latency is critical. Do you use L4 or L7?

**Pause. Think about it...**

**Answer:** **L4.** You don't need to inspect HTTP paths. You need to spread TCP connections across game servers. Fast. Minimal overhead. L7 would parse every packet, add latency. L4 just looks at IP and port, forwards. NLB, HAProxy in TCP mode, or an L4 virtual server. Save the CPU. Save the microseconds. L7 is for when you need to *route by content*. Gaming usually doesn't.

---

## What Could Go Wrong? (Mini Disaster Story)

A team put an L7 load balancer in front of a high-throughput logging service. Millions of log events per second. Each event: small UDP packet. The L7 balancer tried to parse them as HTTP. Failed. Fell back to generic handling. CPU spiked. Latency jumped from 1ms to 50ms. Logs backed up. Downstream systems starved. The fix? Switch to **L4**. The logging protocol wasn't HTTP. L7 was the wrong tool. They moved to NLB. Problem solved. Lesson: L7 is powerful. But if you don't need path-based routing, L4 is faster and simpler. Don't over-engineer.

---

## Surprising Truth / Fun Fact

**Envoy**—the proxy used in **service meshes** like Istio—operates primarily at L7. It can do sophisticated routing: retries, circuit breaking, rate limiting, all based on HTTP headers and paths. But Envoy also has L4 filters for raw TCP. The same proxy can do both. The layer you use depends on the listener config.

---

## Quick Recap (5 bullets)

- **L4:** Transport layer. IP + port. No content inspection. Fast. Use for throughput, streaming, gaming.
- **L7:** Application layer. HTTP. URL, headers, cookies. Path-based routing. SSL termination. Use for microservices, APIs.
- **AWS NLB** = L4. **AWS ALB** = L7. Different tools.
- **HAProxy:** `mode tcp` = L4. `mode http` = L7.
- **Trade-off:** L4 = speed. L7 = intelligence. Pick the layer that matches your needs.

---

## One-Liner to Remember

> **L4 looks at the envelope. L7 opens it and reads the letter. Both distribute—one just looks deeper.**

---

## Next Video

You're using L7. Path-based routing. But wait—your app has sessions. Shopping cart in memory. User logs in on Server 2. Next request goes to Server 4. Cart is gone. You need **sticky sessions**—same user, same server. How do they work? When to use them. When to avoid them. Next: Sticky sessions.
