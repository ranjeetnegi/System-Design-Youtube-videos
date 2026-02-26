# Structured Logging: Logs That Machines Can Read

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

Your app is broken. You need to find why. You grep the logs. "User login failed." Which user? "Error at line 42." Which request? "Something went wrong." What exactly? Your logs are **unstructured**—plain text, human-written, impossible to parse at scale. You're searching for a needle in a haystack. There's a better way: **structured logging**. JSON. Key-value pairs. Machines can read it. You can query it. Filter by user. Trace a request across services. Let me show you.

---

## The Story

**Unstructured logs** are plain text. `"User login failed for john@example.com"`. A human can read it. A machine can't easily parse it. Want to find all failed logins for user X? Grep. Regex. Hope you don't miss variations. At scale—millions of log lines—it breaks. **Structured logs** are machine-readable. JSON: `{"level":"ERROR","user_id":"123","action":"login","error":"invalid_password","timestamp":"2024-01-15T10:30:00Z"}`. Now you can query: `user_id=123 AND action=login AND level=ERROR`. Fast. Precise.

**Log levels:** **DEBUG**—verbose, for development. **INFO**—normal operations. **WARN**—something unexpected but not fatal. **ERROR**—something failed. **FATAL**—system unusable. Use them consistently. Don't log everything as INFO. Filtering by level matters. In production, you might set DEBUG off. INFO and above only. Reduces noise and cost.

**What to log:** **Request ID**—trace a request across services. **User ID**—who was affected? **Timestamp**—when? **Action**—what happened? **Duration**—how long? **Error**—what went wrong? **Context**—relevant IDs, status codes. **What NOT to log:** Passwords. Credit cards. PII (unless necessary and masked). Secrets. API keys. Logging sensitive data is a compliance nightmare. And a security risk.

**Correlation IDs:** In distributed systems, one request hits many services. Each logs. How do you tie them together? **Correlation ID** (or trace ID)—a unique ID per request. Pass it through headers. Every service logs it. Now you can trace the full path. "Show me all logs for request abc-123." You see the journey. That's **distributed tracing**—structured logs are the foundation.

---

## Another Way to See It

Think of a library. Unstructured logs are books with no index—you flip through pages to find a topic. Structured logs are a **card catalog**—every book has metadata: author, subject, year. You search the catalog. You find the book. Same idea. Structured logs are the catalog. You search by keys. You find the right log line. Fast.

---

## Connecting to Software

- **Structured logs** = JSON or key-value. Machine-readable. Queryable. Filter by user_id, request_id, level.
- **Log levels:** DEBUG, INFO, WARN, ERROR, FATAL. Use consistently.
- **What to log:** Request ID, user ID, timestamp, action, duration, error. **NOT:** passwords, PII, credit cards.
- **Correlation IDs** = unique ID per request. Pass through headers. Trace across services. Foundation for distributed tracing.
- **Tools:** ELK stack (Elasticsearch, Logstash, Kibana), Splunk, Datadog, CloudWatch, Loki.
- **Real-world:** Netflix, Uber use structured logging at scale. Essential for debugging distributed systems.

---

## Let's Walk Through the Diagram

```
    UNSTRUCTURED vs STRUCTURED LOGGING

    UNSTRUCTURED (hard to query)              STRUCTURED (queryable)
    ─────────────────────────────             ─────────────────────

    "User login failed"                       {"level":"ERROR",
    "Error at 10:30"                            "request_id":"req-abc",
    "Something went wrong"                      "user_id":"u-123",
                                                "action":"login",
    Grep. Regex. Hope.                           "error":"invalid_password",
                                                 "timestamp":"2024-01-15T10:30:00Z"}

                                                 Query: request_id=req-abc
                                                 Filter: user_id=u-123, level=ERROR
                                                 Trace: all services for req-abc
```

---

## Real-World Examples (2-3)

**1. Netflix.** Millions of requests. Distributed across hundreds of services. Structured logging is mandatory. Every log has request ID, service name, timestamp. They use **ELK** and custom tooling. When something breaks, they trace the request. Find the failure. Fix it. Without structured logs, they'd be blind.

**2. Uber.** Similar scale. Every service logs in JSON. Correlation IDs flow through the stack. They built **Jaeger** (open-source distributed tracing) and use it with structured logs. A single request might hit 20+ services. They can trace all of them. Debug in minutes, not hours.

**3. Stripe.** Handles payments. Compliance matters. They log extensively—but never sensitive data. Structured logs with request IDs, merchant IDs, error codes. Auditable. Queryable. When a merchant reports an issue, they can trace the exact request. Structured logging enables that.

---

## Let's Think Together

**Question:** Your team logs `user_id` in every log line. A compliance audit says: "You're logging PII. Users can request deletion." What do you do?

**Pause. Think about it...**

**Answer:** Options: (1) **Hash the user_id**—store a hash, not the raw ID. You can still correlate logs for the same user. But you can't reverse it. (2) **Don't log user_id**—log request_id only. When you need to find a user's logs, look up request_id from your app layer. (3) **Retention policy**—delete logs after X days. Reduce exposure. (4) **Access control**—limit who can query logs. Compliance often requires a combination. The key: know what you log. Don't log what you can't protect or delete.

---

## What Could Go Wrong? (Mini Disaster Story)

A company logged "full request payload" for debugging. Convenient. One day, a bug sent credit card numbers in the request body. They were logged. Plain text. A breach. PCI violation. Fines. Reputation damage. The fix: never log request bodies by default. Log metadata—endpoint, method, status. If you need payload for debugging, redact. Strip sensitive fields. Or don't log it at all. The lesson: **what you log is what you protect**. Assume logs can leak. Don't put secrets in them.

---

## Surprising Truth / Fun Fact

**Structured logging** took off with the rise of **microservices** and **cloud-native** systems. In a monolith, you had one log file. Grep worked. With 100 services, each with its own logs, you need a different approach. **ELK** (Elasticsearch, Logstash, Kibana) and **Splunk** made structured logs searchable at scale. Now it's standard. **Datadog**, **Grafana Loki**—all assume JSON logs. The industry shifted from "logs for humans" to "logs for machines." Humans still read them. But machines do the heavy lifting.

---

## Quick Recap (5 bullets)

- **Structured logs** = JSON or key-value. Machine-readable. Queryable. Essential at scale.
- **Log levels:** DEBUG, INFO, WARN, ERROR, FATAL. Use consistently.
- **What to log:** Request ID, user ID, timestamp, action, duration, error. **NOT:** passwords, PII, secrets.
- **Correlation IDs** = trace a request across services. Foundation for distributed tracing.
- **Tools:** ELK, Splunk, Datadog, Loki. Structured logs are the input.

---

## One-Liner to Remember

> **Structured logging: logs machines can read. JSON. Key-value. Query by request_id, user_id, level. Never log secrets.**

---

## Next Video

You have logs. You have metrics. But when should you wake someone up at 3 AM? And when should you just create a ticket? That's **alerting strategy**. That's next.
