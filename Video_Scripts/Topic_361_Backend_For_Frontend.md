# Backend for Frontend (BFF): One API Per Client

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

Your API serves a mobile app and a web app. Mobile needs lightweight responses—small payloads, fewer fields. Web needs rich data—full product details, related items, recommendations. You build one generic API. Mobile over-fetches (gets data it doesn't need). Web under-fetches (needs multiple round-trips). Or you add query params: `?platform=mobile` vs `?platform=web`. Your API becomes a mess of conditionals. There's a cleaner way: **Backend for Frontend (BFF)**—a dedicated backend per client type. Mobile BFF. Web BFF. Each tailors responses. Let me show you how.

---

## The Story

**Backend for Frontend (BFF)** means you have a separate API layer for each type of client. Mobile app? Mobile BFF. Web app? Web BFF. Smart TV? TV BFF. Each BFF talks to your core microservices. It aggregates, filters, and shapes the response for *that* client. The mobile BFF returns minimal data—IDs, names, thumbnails. The web BFF returns full objects, nested relations, SEO metadata. Same underlying services. Different presentations.

**Why?** Different clients have different needs. Mobile: bandwidth matters. Battery matters. Smaller screens. Simpler UI. Web: more data. Richer interactions. TV: different layout. Different navigation. One generic API either over-serves (mobile gets bloat) or under-serves (web needs many calls). The BFF is the **adapter**. It adapts the backend to the frontend. Each frontend team owns their BFF. They can iterate without coordinating with other clients.

**Trade-offs:** Code duplication. Logic for "get user" might exist in mobile BFF and web BFF. But the logic is thin—mostly aggregation and shaping. The heavy logic stays in core services. The BFF is a thin layer. More services to maintain. But clearer boundaries. **Comparison to GraphQL:** GraphQL lets the client request exactly what it needs. One API. Client-driven. BFF is server-driven—the BFF decides the shape. Both solve over/under-fetching. GraphQL is flexible. BFF is explicit. You can combine: a BFF that exposes GraphQL for the web. Different tools, same goal.

---

## Another Way to See It

Think of a restaurant with one menu. A kid wants a simple plate. An adult wants the full course. A vegan needs modifications. One menu = compromises. Or: a kids' menu, an adult menu, a vegan menu. Each tailored. The kitchen (core services) has the ingredients. The menus (BFFs) present them differently. Backend for Frontend is the menu per customer type. Same kitchen. Different presentations.

---

## Connecting to Software

- **BFF** = dedicated backend per client type. Mobile BFF, Web BFF, TV BFF.
- **Purpose:** Tailor responses. Avoid over-fetching (mobile) and under-fetching (web).
- **BFF** aggregates from core microservices. Shapes data. Returns client-optimized payloads.
- **Trade-offs:** Code duplication (thin logic). More services. Benefits: client independence, optimized responses.
- **vs GraphQL:** GraphQL = one API, client requests shape. BFF = server decides shape per client. Can combine.
- **Real tools:** Node.js, Go, or any language. Often same stack as frontend (e.g., Node for web BFF if web is React).

---

## Let's Walk Through the Diagram

```
    CLIENTS                         BFF LAYER                    CORE SERVICES
    =======                         =========                    =============

    ┌──────────┐                    ┌──────────────┐
    │ Mobile   │ ──────────────────►│ Mobile BFF   │ ──┬──► User Service
    │ App      │   Optimized for    │ - Minimal    │   ├──► Product Service
    │          │   small payloads   │   payloads   │   └──► Order Service
    └──────────┘                    └──────────────┘
                                           │
    ┌──────────┐                    ┌──────────────┐
    │ Web App  │ ──────────────────►│ Web BFF      │ ──┬──► User Service
    │          │   Rich data,       │ - Full       │   ├──► Product Service
    │          │   SEO, nested      │   objects    │   └──► Order Service
    └──────────┘                    └──────────────┘
                                           │
    ┌──────────┐                    ┌──────────────┐
    │ TV App   │ ──────────────────►│ TV BFF       │ ──┬──► Same services
    │          │   Different layout │ - TV-optimized│   └──► Different shape
    └──────────┘                    └──────────────┘
```

---

## Real-World Examples (2-3)

**1. Netflix.** Separate APIs for TV, mobile, and web. Each client has different constraints. TV: big screen, remote control, different navigation. Mobile: touch, smaller screen, bandwidth. Web: mouse, keyboard, SEO. Netflix BFFs tailor the experience. Same content. Different delivery. They've talked about this in their tech blog.

**2. SoundCloud.** Uses BFF pattern. Mobile app has a mobile-specific API. Web has a web-specific API. Each optimized for its client. Reduces payload size on mobile. Enables rich features on web. Independent release cycles for mobile and web teams.

**3. Spotify.** Multiple clients—desktop, mobile, web, TV, car. Each has a BFF or BFF-like layer. Client teams own their API surface. They can ship without blocking other clients. BFF enables that independence.

---

## Let's Think Together

**Question:** You have a mobile BFF and a web BFF. Both need "get user profile." Do you duplicate the logic?

**Pause. Think about it...**

**Answer:** The *aggregation* logic might differ. Mobile: return name, avatar, 5 recent items. Web: return full profile, all orders, preferences. But the *core* logic—fetch user from User Service—is the same. You can share that in a library. Or the BFFs call the same User Service. The duplication is in *shaping*—what fields, what nesting. That's thin. Accept some duplication for clarity. Or extract a shared "user profile" service that both BFFs call—but then you're back to one API. The BFF's value is client-specific shaping. A little duplication is OK. Don't over-abstract.

---

## What Could Go Wrong? (Mini Disaster Story)

A company built a mobile BFF and a web BFF. They worked. Then they added a third-party partner API. "Partners need our data too." They built a Partner BFF. Then internal tools. Internal BFF. Then a public API. Public BFF. Soon: 6 BFFs. Each had similar logic. Bug fix: change in 6 places. They forgot the BFF rule: one per *client type*, not one per *stakeholder*. The fix? Consolidate. Partner + Public might share a BFF. Internal tools might use the web BFF with different auth. Don't multiply BFFs unnecessarily. Lesson: BFF per client *type*. Not per team or per integration. Keep the number manageable.

---

## Surprising Truth / Fun Fact

The term **BFF** was coined by **Sam Newman** (author of "Building Microservices") and **Phil Calçado** at SoundCloud. They needed to support web and mobile with different needs. One API wasn't working. They created a backend per frontend. The pattern spread. Today, many companies use it without knowing the name—they just "have a mobile API and a web API." Same idea.

---

## Quick Recap (5 bullets)

- **BFF** = Backend for Frontend. One API per client type (mobile, web, TV).
- **Problem:** One API = over-fetching or under-fetching. Different clients need different shapes.
- **Solution:** Each BFF tailors responses. Aggregates from core services. Client-optimized.
- **Benefits:** Client independence. Optimized payloads. Teams can iterate without blocking.
- **Trade-offs:** Code duplication (thin). More services. vs GraphQL: different approach, same goal.

---

## One-Liner to Remember

> **BFF: one backend per frontend type. Mobile gets mobile-shaped data. Web gets web-shaped data. Same services, different presentations.**

---

## Next Video

Each microservice has its own data. But should each service have its own database? Or share one? That's the **database-per-service** pattern. That's next.
