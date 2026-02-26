# Writing RFCs and Design Documents

## Video Length: ~4-5 minutes | Level: Senior-Staff

---

## The Hook (20-30 seconds)

You have an idea. A big one. Migrate the monolith to microservices. Change the database. Rearchitect the auth flow. You walk into a meeting. You talk. People nod. Two weeks later, nobody remembers what you decided. Or why. Someone asks: "Did we consider option B?" You did. In the meeting. It's gone. There's a better way: **RFCs**—Request for Comments. A written proposal. A permanent record. Async review. Scales across teams. Let me show you how to write one that gets buy-in.

---

## The Story

An **RFC**—Request for Comments—is a written proposal for a technical decision. It's not a spec. It's a *proposal*. You're asking for feedback. You spell out the problem. You propose a solution. You list alternatives. You document trade-offs and risks. You get input. You iterate. Then you decide. The RFC becomes the **permanent record**—why we chose X, what we considered, what we rejected.

**Why writing beats talking:** Meetings are ephemeral. People forget. Time zones make sync hard. Written docs are **async**—review when you can. They **scale**—10 people or 100, everyone reads the same thing. They're **searchable**—"why did we pick Postgres?" Search the RFC. They force clarity—if you can't write it, you might not understand it. Writing exposes gaps. Talking hides them.

**Key sections:** **Problem statement**—what's broken? What's the pain? **Proposed solution**—what do you recommend? **Alternatives considered**—what else did you look at? **Trade-offs**—what do we gain? What do we lose? **Risks**—what could go wrong? **Rollout plan**—how do we ship this? Phases? Feature flags? Each section answers a question your reviewers will ask. Anticipate. Write it down.

**Getting buy-in:** Share early. Don't surprise people with a 50-page doc. One-pager first. Get alignment on the problem. Then expand. Invite dissenters—"what am I missing?" Address their concerns in the doc. Make it easy to comment. Google Docs, Notion, Confluence. Track feedback. Update the RFC. Show you listened. The goal isn't to win. It's to make the best decision.

---

## Another Way to See It

Think of an RFC like a **business plan** for a technical change. A startup pitch deck: problem, solution, market (alternatives), risks, roadmap. Investors (your peers, your leadership) need to understand before they commit. You're not asking for money. You're asking for engineering time, coordination, and buy-in. The RFC is your pitch. Make it clear. Make it compelling. Make it honest about the downsides.

---

## Connecting to Software

- **RFC** = Request for Comments. A written proposal for a technical decision. Not a spec—a proposal for review.
- **Why write:** Async review, permanent record, scales across teams. Writing forces clarity.
- **Sections:** Problem statement, proposed solution, alternatives considered, trade-offs, risks, rollout plan.
- **Getting buy-in:** Share early. One-pager first. Invite dissenters. Make feedback easy. Update the doc.
- **When to write:** Big changes, cross-team impact, architectural decisions, migrations. Not for small tweaks.
- **Real-world:** Google design docs, Uber RFCs, Stripe RFC process. Many companies use RFCs or similar (ADR—Architecture Decision Records).

---

## Let's Walk Through the Diagram

```
    RFC LIFECYCLE

    ┌─────────────┐     ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
    │  Problem    │────►│  Draft RFC  │────►│  Review     │────►│  Decision   │
    │  Identified │     │  Written    │     │  & Feedback │     │  & Approval │
    └─────────────┘     └─────────────┘     └─────────────┘     └─────────────┘
                              │                    │                    │
                              │                    │                    │
                              ▼                    ▼                    ▼
                       ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
                       │ - Problem   │     │ - Async     │     │ - Approved  │
                       │ - Solution  │     │   comments  │     │   RFC =     │
                       │ - Alts      │     │ - Revisions │     │   permanent │
                       │ - Risks     │     │ - Consensus │     │   record    │
                       └─────────────┘     └─────────────┘     └─────────────┘
```

---

## Real-World Examples (2-3)

**1. Google.** Design docs are core to how Google operates. Every significant change gets a design doc. Problem, proposal, alternatives, trade-offs. Reviewers add comments. Authors respond. The doc lives in the codebase or internal wiki. Years later, you can find why a system was built a certain way. It's part of their culture—"write it down."

**2. Uber.** Uses RFCs for major technical decisions. Cross-team changes, migrations, new architectures. RFCs go through review. Stakeholders from affected teams weigh in. The process ensures alignment before code is written. Reduces surprises. Reduces rework.

**3. Stripe.** Has a formal RFC process for API changes and architectural decisions. RFCs are public (or shared widely). They get feedback from internal and sometimes external stakeholders. The process has helped them maintain API consistency and avoid breaking changes.

---

## Let's Think Together

**Question:** You've written an RFC. Someone comments: "What about option C? You didn't consider it." You did consider it—and rejected it. How do you handle this?

**Pause. Think about it...**

**Answer:** Add it to the RFC. Create an "Alternatives Considered" section (or expand it). Write: "Option C: [description]. We rejected it because [reason]." This does two things: it shows you did the work, and it prevents the same question from coming up again. The RFC is the source of truth. If it's not in the doc, it didn't happen. Document your rejections. Future you will thank you.

---

## What Could Go Wrong? (Mini Disaster Story)

A team wrote an RFC to migrate from MySQL to PostgreSQL. The doc was thorough. Problem, solution, migration plan. They got approval. They started. Six months in, they hit a wall: a critical feature relied on MySQL-specific behavior. The RFC hadn't called it out. Nobody had flagged it in review. The migration stalled. Cost overruns. Delayed launch. The lesson? **Call out assumptions.** "We assume no MySQL-specific features." List them. Have someone audit. RFCs aren't just for the happy path. Document the edge cases. The "we're assuming X" statements. They're landmines if you don't.

---

## Surprising Truth / Fun Fact

The term **RFC** comes from the **Internet Engineering Task Force (IETF)**. RFC 1 was written in 1969—"Host Software" by Steve Crocker. It described the first host-to-host protocol for ARPANET. Today, RFCs like HTTP/1.1 (RFC 2616) and TCP (RFC 793) are foundational. Companies adopted the name because it captures the spirit: a document open for comment, not a decree. You're requesting feedback. You're not commanding. That humility is built into the name.

---

## Quick Recap (5 bullets)

- **RFC** = Request for Comments. A written proposal for a technical decision. Permanent record, async review.
- **Why write:** Meetings forget. Docs scale. Writing forces clarity. Exposes gaps.
- **Sections:** Problem, solution, alternatives, trade-offs, risks, rollout plan.
- **Getting buy-in:** Share early. Invite dissenters. Document rejections. Make feedback easy.
- **When to write:** Big changes, cross-team impact. Document assumptions and edge cases.

---

## One-Liner to Remember

> **RFC: write it down. Problem, solution, alternatives, risks. Get feedback. Make it the permanent record.**

---

## Next Video

You wrote the RFC. You shipped the feature. But now there's a mountain of shortcuts, workarounds, and hacks in the codebase. That's **technical debt**. How do you manage it? When do you pay it down? When do you live with it? That's next.
