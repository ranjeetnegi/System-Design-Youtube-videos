# Service Discovery: How Services Find Each Other

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

You're new in town. You need a plumber. You could memorize one plumber's address—but what if they move? Or go out of business? Better: you look up "plumbers" in a directory. The directory has current listings. You get an address. You call. **Service discovery** is that directory. Your service needs to call the "payment service." The payment service's IP changes—new pods, new instances, scaling. You don't hardcode IPs. You ask the directory: "Where is payment-service?" You get the current address. You call. Dynamic. Always up to date.

---

## The Story

In a **microservices** world, services talk to each other. Order service calls Payment service. Payment calls User service. But **IPs and ports change.** Containers restart. Kubernetes reschedules pods. Auto-scaling adds and removes instances. Hardcoding `http://10.0.1.5:8080` is a recipe for failure. Tomorrow that IP might be a different service. Or nothing.

**Service discovery** solves this. A **service registry** holds the mapping: service name → list of healthy instances (IP:port). Services register when they start. They deregister when they stop. Or they heartbeat—"I'm alive." Clients query: "Give me instances of payment-service." They get a list. They pick one (round-robin, random, etc.). They call. **Client-side discovery:** The client asks the registry. The client chooses the instance. **Server-side discovery:** The client asks a load balancer or proxy. The proxy knows the instances. Client doesn't. Simpler client. Proxy does the discovery.

**Tools:** **Consul** (HashiCorp)—service registry, health checks, DNS. **etcd**—key-value store, used by Kubernetes. **ZooKeeper**—older, still used by Kafka. **Kubernetes** has built-in service discovery—every Service gets a DNS name. `payment-service.namespace.svc.cluster.local`. Kube-proxy or the cloud load balancer routes to healthy pods. No external registry needed. **Eureka** (Netflix)—client-side discovery, used with Spring Cloud. **AWS ECS** has service discovery—integrated with Route 53 or Cloud Map.

**Health checks** matter. A dead instance should be removed from the registry. Otherwise clients get 503s. Heartbeat. Or active health checks—periodically hit /health. Unhealthy? Deregister. Discovery is only useful if the list is accurate.

---

## Another Way to See It

A phone book. Businesses change numbers. Move. Close. The phone book updates. You don't memorize. You look up. Service registry = phone book. Service name = business name. Instances = current phone numbers. You look up. You call. The registry keeps the book current. You always get a valid number—or none, if the business closed. Health checks = "is this number still in service?"

---

## Connecting to Software

- **Service registry** = stores service name → list of (IP, port). **Consul**, **etcd**, **ZooKeeper**, **Eureka**.
- **Client-side discovery:** App queries registry. App chooses instance. App calls directly. More logic in client. **Netflix Eureka** model.
- **Server-side discovery:** App calls a fixed endpoint (load balancer). LB has the instance list. **Kubernetes Service**, **AWS ALB**.
- **Kubernetes:** Service object. DNS name. `payment-service`. Kube-proxy routes to pod IPs. Built-in. No separate registry for in-cluster calls.
- **Health checks:** Registry must know which instances are healthy. Heartbeat (instance pings registry) or active check (registry pings instance). Unhealthy = removed from pool.
- **DNS-based discovery:** Some systems use DNS—add/remove A records as instances change. **Consul** can serve DNS. **AWS Cloud Map**.

---

## Let's Walk Through the Diagram

```
    CLIENT-SIDE DISCOVERY              SERVER-SIDE DISCOVERY

    Order Service                      Order Service
         │                                  │
         │  "payment-service instances?"    │  "POST /charge"
         ▼                                  ▼
    ┌─────────────┐                    ┌─────────────┐
    │   Registry  │                    │ Load Balancer│
    │  (Consul,   │                    │ (K8s Svc,   │
    │   Eureka)   │                    │  ALB)       │
    └──────┬──────┘                    └──────┬──────┘
           │                                  │
           │  [10.0.1.5:8080, 10.0.1.6:8080]  │  has instance list
           ▼                                  ▼
    Pick one, call directly              LB routes to instance
         │                                  │
         ▼                                  ▼
    Payment Service (instance)         Payment Service
```

**Left:** Client does discovery. **Right:** Load balancer does it. Client just calls. Simpler. Both work. Choose by operational model.

---

## Real-World Examples (2-3)

**Example 1 — Netflix Eureka:** Netflix's services register with Eureka. Client libraries query Eureka for instance lists. Client-side discovery. Ribbon (load balancer) picks an instance. Used at massive scale. Services find each other without config files.

**Example 2 — Kubernetes:** You deploy payment-service. 3 pods. You create a Service. Other pods call `http://payment-service`. Kubernetes DNS resolves to the Service's cluster IP. Kube-proxy forwards to a healthy pod. No Eureka. No Consul. Built-in. Server-side. The Service is the abstraction.

**Example 3 — AWS ECS with Cloud Map:** ECS tasks register in AWS Cloud Map. Service name: `payment`. Other services resolve `payment.local` via DNS. Cloud Map returns healthy task IPs. Integrated with ECS. Managed. No self-hosted registry.

---

## Let's Think Together

You have 10 microservices. Each needs to call 3–4 others. Do you use a service registry, or hardcode IPs in config?

Pause and think.

Registry. Or Kubernetes Services. Hardcoding fails the moment you scale, restart, or migrate. One config change, 10 services to update. With discovery: services register. Clients query. Dynamic. Survives pod churn. Use Consul, Eureka, or K8s built-in. Never hardcode IPs in a dynamic environment.

---

## What Could Go Wrong? (Mini Disaster Story)

A team hardcoded the payment service IP in 20 config files. They migrated to new servers. New IPs. They updated 18 files. Missed 2. Two services kept calling the old IP. Old server was decommissioned. Connection refused. Silent failures. Orders went through. Payments failed. Revenue loss. Support chaos. Lesson: hardcoded IPs don't scale. One missed update = production incident. Service discovery: update the registry. All clients get new IPs automatically. No config drift.

---

## Surprising Truth / Fun Fact

Kubernetes didn't invent service discovery. But it made it default. Before K8s, you needed Consul or Eureka or similar. Now, if you're on Kubernetes, you get it for free. Every Service is a discovery endpoint. The platform does the work. That's why "just use Kubernetes" is often the answer—it solves discovery, load balancing, and health checks in one abstraction.

---

## Quick Recap (5 bullets)

- **Service discovery** = how services find each other when IPs change. Registry maps name → instances.
- **Client-side:** Client queries registry, picks instance, calls. **Server-side:** Client calls LB, LB routes. Both valid.
- **Tools:** Consul, etcd, ZooKeeper, Eureka. **Kubernetes:** built-in Service + DNS.
- **Health checks** = registry must know healthy instances. Heartbeat or active check. Unhealthy = removed.
- **Never hardcode IPs** in dynamic environments. Discovery is essential at scale.

---

## One-Liner to Remember

> Service discovery: the directory that tells you where a service lives. IPs change. The registry doesn't. Query it. Never hardcode.

---

## Next Video

You've heard of Docker. Kubernetes. Containers. But what are they, really? And why does **orchestration** matter? Next: **Containers and orchestration**—packaging and running your services at scale. See you there.
