# HTTP/2 and HTTP/3: What Changed and Why

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

Imagine a single-lane highway. One car at a time. If a truck breaks down, every car behind it waits. No passing. No alternate route. That was HTTP/1.1. Every website opened dozens of connections—one lane per request. Then came HTTP/2: multiple lanes on the same road. Then HTTP/3: a whole new road built on a different foundation. The web got faster. Here's what actually changed.

---

## The Story

**HTTP/1.1** had three big problems. First: **head-of-line blocking**. One request per connection. If request 1 is slow—maybe a big image—request 2 waits. Request 3 waits. The whole pipeline stalls. Second: **one request per connection**. Browsers opened 6–8 connections per domain. Each connection: TCP handshake, TLS handshake. Third: **many TCP connections**. Each connection consumes memory. Servers hit connection limits. The web got heavier. Pages needed hundreds of requests. HTTP/1.1 wasn't built for that.

**HTTP/2** fixed most of it. **Multiplexing**: multiple streams over one TCP connection. Request 1, 2, 3, 4—all flying in parallel on the same pipe. No more "one at a time." **Header compression (HPACK)**: HTTP headers repeat. "Cookie: session=abc123" sent with every request. HPACK compresses them. Smaller packets. **Server push**: the server can push CSS and JS before the browser asks. One round trip instead of "request HTML, parse, request CSS, request JS." **Binary framing**: no more text parsing. Frames are binary. Faster to process. But here's the catch: HTTP/2 still runs over TCP. If one packet drops, TCP blocks the whole connection until it's retransmitted. Head-of-line blocking at the transport layer.

**HTTP/3** changes the foundation. Built on **QUIC**—a protocol that runs over **UDP**, not TCP. Each stream has its own delivery. Packet loss on stream 1? Stream 2 keeps going. **TCP head-of-line blocking eliminated**. **Faster connection setup**: QUIC combines the handshake. First connection: 1 RTT. Reconnection: **0-RTT**—send data immediately, no round trip. Critical for mobile users switching networks. HTTP/3 is HTTP/2 semantics over QUIC. Same features. Better transport.

---

## Another Way to See It

HTTP/1.1 is a restaurant with one waiter and one order at a time. You order. Waiter goes to kitchen. Comes back. Next person orders. HTTP/2: one waiter, but they take five orders at once, carry five plates. Multiplexing. HTTP/3: the kitchen has five separate windows. One order delayed? The other four still come out. No single bottleneck.

---

## Connecting to Software

- **HTTP/1.1 problems:** Head-of-line blocking (one slow request blocks others), one request per connection, 6–8 connections per domain, repeated headers (no compression).
- **HTTP/2:** Multiplexing (multiple streams over one TCP connection), **HPACK** header compression, **server push**, binary framing. Supported by **NGINX**, **Cloudflare**, **AWS ALB**, **CDNs**.
- **HTTP/3 (QUIC):** Built on **UDP**. Eliminates TCP head-of-line blocking. **0-RTT** for reconnections. Used by **Google** (Chrome, YouTube), **Cloudflare**, **Facebook**. IETF standard since 2022.
- **When to use:** HTTP/2 is widely supported—use it. HTTP/3 is newer; enable if your stack supports it (Cloudflare, NGINX 1.25+). Mobile, high-latency networks benefit most from HTTP/3's 0-RTT.

---

## Let's Walk Through the Diagram

```
HTTP/1.1:
  Connection 1: [Req1] -------- [Resp1] -------- [Req2] -------- [Resp2]
  Connection 2: [Req3] -------- [Resp3] ...
  (6-8 connections, one request at a time per connection, head-of-line blocking)

HTTP/2 (one TCP connection):
  Stream 1: [Req1] ---- [Resp1]
  Stream 2:     [Req2] ---- [Resp2]
  Stream 3:         [Req3] ---- [Resp3]
  Stream 4:             [Req4] ---- [Resp4]
  (Multiplexed! But TCP packet loss blocks ALL streams)

HTTP/3 (QUIC over UDP):
  Stream 1: [Req1] --X-- [retransmit] [Resp1]   (packet loss, stream 1 waits)
  Stream 2: [Req2] ------------ [Resp2]        (stream 2 unaffected!)
  Stream 3: [Req3] ------------ [Resp3]
  (Independent streams—packet loss on one doesn't block others)
```

---

## Real-World Examples (2-3)

**1. Google:** Pioneered SPDY (precursor to HTTP/2) and QUIC. YouTube, Search, Gmail—all use HTTP/2 and HTTP/3 where available. Google's data showed 3–15% faster page loads with QUIC on mobile. They pushed the standards.

**2. Cloudflare:** One of the first to offer HTTP/3 at the edge. Enable it with a checkbox. Automatic QUIC negotiation. If the client supports it, you get HTTP/3. If not, fallback to HTTP/2 or HTTP/1.1. Millions of sites use it.

**3. CDNs and APIs:** AWS CloudFront, Fastly, Akamai—all support HTTP/2. HTTP/3 adoption growing. For API-heavy apps, multiplexing means one connection handles hundreds of concurrent requests. Fewer connections, less overhead.

---

## Let's Think Together

**Question:** You're building a dashboard that loads 50 API calls on page load. HTTP/1.1 would open 6–8 connections, so those 50 requests queue. With HTTP/2, how many connections do you need?

**Pause. Think about it...**

**Answer:** One. HTTP/2 multiplexes all 50 requests over a single connection. One TCP handshake. One TLS handshake. Fifty streams. That's the win. With HTTP/1.1, you'd need 6–8 connections and still queue. HTTP/2 collapses that to one pipe.

---

## What Could Go Wrong? (Mini Disaster Story)

A team enabled HTTP/2 and saw *slower* performance. Confused. They dug in. Their load balancer was terminating HTTP/2 and opening new HTTP/1.1 connections to backend servers. So: client → LB (HTTP/2, one connection) → backend (HTTP/1.1, many connections). The backend was drowning in connections. The LB became a funnel: 1 connection in, 50 out. Connection exhaustion. Timeouts. The fix: enable HTTP/2 end-to-end, or use connection pooling at the LB. HTTP/2 only helps if you use it all the way.

---

## Surprising Truth / Fun Fact

**QUIC** was originally "Quick UDP Internet Connections"—a Google project. It was so successful that the IETF adopted it, renamed it, and made it the basis of HTTP/3. The same protocol now powers Chrome's connection to Google servers and many CDNs. One research project became a web standard.

---

## Quick Recap (5 bullets)

- **HTTP/1.1:** One request per connection, head-of-line blocking, many connections. The old way.
- **HTTP/2:** Multiplexing (many streams, one connection), HPACK compression, server push, binary framing. Still over TCP.
- **HTTP/3:** Built on QUIC (UDP). Eliminates TCP head-of-line blocking. 0-RTT reconnection.
- **When to use:** HTTP/2 is default. HTTP/3 for mobile, high-latency, or when your stack supports it.
- **Real adoption:** Google, Cloudflare, major CDNs. Enable both; clients negotiate.

---

## One-Liner to Remember

> HTTP/1.1 was one lane. HTTP/2 added more lanes on the same road. HTTP/3 built a new road where one pothole doesn't stop everyone.

---

## Next Video

Your API works. Your database is connected. But your page loads slowly because one query triggers 100 more queries. That's the **N+1 problem**. That's next.
