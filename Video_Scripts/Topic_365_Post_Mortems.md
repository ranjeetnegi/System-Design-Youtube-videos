# Post-Mortems: Blameless Incident Review

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

The system went down. Users couldn't log in. Orders failed. Revenue lost. Someone pushed a bad deploy. Or someone misconfigured the database. Your first instinct: find who. Blame them. Make sure it never happens again. But here's the thing: **blame kills learning**. If people fear punishment, they hide mistakes. They don't report. They don't share. The next incident will be worse. There's a better way: **blameless post-mortems**. Focus on systems, not people. Learn. Improve. Let me show you how.

---

## The Story

A **post-mortem** is a structured review after an incident. Not a witch hunt. A learning exercise. What happened? Why? What can we do to prevent it? The goal is **improvement**, not punishment. **Blameless culture** means we assume people acted with good intent. The system allowed the mistake. The process failed. The tooling failed. The person was the last link in a chain. Fix the chain.

**Standard sections:** **Timeline**—what happened, when? Order matters. **Impact**—how many users? How long? Revenue? **Root cause**—what was the primary trigger? **Contributing factors**—what made it worse? Lack of tests? No rollback? **Action items**—what will we change? Specific. Assignable. With deadlines. Each section is a lens. Together they tell the story.

**The 5 Whys:** Ask "why" five times. "The server crashed." Why? "CPU spiked." Why? "A query ran wild." Why? "No query timeout." Why? "We didn't configure it." Why? "We didn't have runbooks for that." Root cause: missing runbook and configuration. The 5 Whys drill down. Don't stop at "someone made a mistake." Go deeper. What system allowed it?

**How to run one:** Schedule within 48 hours—memories are fresh. Who attends? Anyone involved. On-call. Developers. SREs. Product if needed. Keep it small enough to be productive. 5–10 people. Use a facilitator—someone neutral. Not the person who "caused" it. Share the doc widely after. Post-mortems are public. Everyone learns. Follow up on action items. A post-mortem without follow-through is theater.

---

## Another Way to See It

Think of a hospital's **morbidity and mortality (M&M)** conference. Doctors review cases where things went wrong. Not to punish. To learn. "What could we have done differently?" The culture is blameless. The goal is better care. Post-mortems are the engineering equivalent. We're not doctors. We're not saving lives. But we're building systems. We make mistakes. The question is: do we learn from them?

---

## Connecting to Software

- **Post-mortem** = structured review after an incident. Goal: learn, improve. Not blame.
- **Blameless culture** = focus on systems, not people. Assume good intent. Fix the process.
- **Sections:** Timeline, impact, root cause, contributing factors, action items.
- **5 Whys** = ask "why" five times to drill to root cause. Don't stop at "someone made a mistake."
- **How to run:** Schedule within 48 hours. Involve relevant people. Facilitator. Share doc widely. Follow up on action items.
- **Real-world:** Google SRE post-mortem culture, PagerDuty incident response. Blameless is industry standard for high-reliability orgs.

---

## Let's Walk Through the Diagram

```
    POST-MORTEM LIFECYCLE

    INCIDENT                    POST-MORTEM                    FOLLOW-UP
    ────────                    ───────────                    ─────────

    ┌─────────────┐             ┌─────────────┐                ┌─────────────┐
    │  Incident   │             │  Write      │                │  Action     │
    │  Resolved   │────────────►│  Post-Mortem│───────────────►│  Items      │
    └─────────────┘             └─────────────┘                └─────────────┘
         │                            │                             │
         │                            │                             │
         ▼                            ▼                             ▼
    - Impact known?             - Timeline                    - Assign owners
    - Root cause?               - Root cause                  - Track completion
    - Who can attend?           - 5 Whys                      - Retrospective
                                - Action items                 - Did we improve?
```

---

## Real-World Examples (2-3)

**1. Google SRE.** Post-mortems are core to their culture. Every significant incident gets one. Blameless. They're shared widely. The SRE book devotes a chapter to it. The philosophy: humans err. Systems should make errors hard. Post-mortems improve systems. They've run thousands. The practice has made Google's systems more reliable.

**2. PagerDuty.** Built a business around incident response. They advocate blameless post-mortems. Their product integrates with post-mortem workflows—link incidents to post-mortem docs, track action items. They've seen customers transform from "who broke it?" to "what can we fix?" Culture shift. Better outcomes.

**3. GitHub.** Had a major outage in 2018. They published a public post-mortem. Transparent. Detailed. No blame. They explained what happened, why, and what they'd do differently. Users appreciated the honesty. It built trust. Public post-mortems can be a strength—if you do them right.

---

## Let's Think Together

**Question:** During a post-mortem, someone says: "Well, if John had run the tests before deploying, this wouldn't have happened." How do you respond?

**Pause. Think about it...**

**Answer:** Redirect. "So the system allowed a deploy without tests passing. Why does our CI allow that? Should we add a gate?" Shift from John to the system. The goal isn't to defend John. It's to find what we can fix. Maybe the fix is: require tests to pass before deploy. Now the system prevents the mistake. John didn't fail. The process failed to protect against human error. That's blameless. Fix the process.

---

## What Could Go Wrong? (Mini Disaster Story)

A company ran post-mortems. But leadership used them to identify "problem" engineers. "Sarah was in 3 post-mortems this quarter. She's a risk." Engineers stopped volunteering for on-call. They hid incidents. "We fixed it. No need for a post-mortem." Small issues became big issues. One engineer left. Wrote a blog: "Our post-mortems were witch hunts." The company's reputation suffered. The fix? Leadership had to commit: post-mortems are blameless. No performance reviews based on incident involvement. Culture change. It took months. The lesson: blameless only works if leadership means it. Words aren't enough. Actions matter.

---

## Surprising Truth / Fun Fact

**Blameless post-mortems** were popularized by **Google's SRE team** and documented in the *Site Reliability Engineering* book. But the concept goes back further—**aviation** and **healthcare** have used similar "just culture" approaches for decades. When a plane crashes, the focus is on what failed—not on punishing the pilot. The goal: make flying safer. Same idea. Engineering adopted it. Now it's table stakes for high-reliability organizations.

---

## Quick Recap (5 bullets)

- **Post-mortem** = structured review after an incident. Goal: learn, improve. Not blame.
- **Blameless** = focus on systems, not people. Assume good intent. Fix the process.
- **Sections:** Timeline, impact, root cause, contributing factors, action items.
- **5 Whys** = drill to root cause. Don't stop at "someone made a mistake."
- **Follow up:** Action items need owners and deadlines. A post-mortem without follow-through is theater.

---

## One-Liner to Remember

> **Post-mortem: blameless. Focus on systems. Learn. Improve. Follow up on action items.**

---

## Next Video

Incidents happen. But how do you find the problem in millions of log lines? You need **structured logging**—consistent, searchable, machine-readable. That's next.
