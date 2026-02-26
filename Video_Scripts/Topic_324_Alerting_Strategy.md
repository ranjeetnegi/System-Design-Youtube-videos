# Alerting Strategy: What to Alert On (And What Not To)

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

Your phone buzzes at 3 AM. Again. "High CPU on server-42." You check. It's a scheduled job. Expected. You go back to sleep. 10 minutes later. "Error in payment service." One error. A retry. It succeeded. Another buzz. "Disk 80% full." On a dev box. Nobody cares. By morning, you've gotten 47 alerts. You've ignored 46. **Alert fatigue**. Too many alerts = you ignore all of them. The one that mattered? Buried. There's a better way: **alert on what matters**. Only when you can act. Let me show you.

---

## The Story

**Alert fatigue** is real. When you're paged constantly for non-issues, you stop responding. You mute. You ignore. Then the real incident happens. You miss it. Or you're too tired to care. The fix: **fewer, better alerts**. Every alert should be **actionable**. If you can't do something about it, don't alert. "CPU high" — so what? "CPU high for 10 minutes, likely runaway process, runbook: restart worker" — now you can act.

**Severity levels:** **P1**—page. Wake someone up. System down. Revenue impact. **P2**—respond within hours. Degraded. Not critical. **P3**—next business day. Fix when you can. **P4**—informational. No immediate action. Use them consistently. P1 should be rare. If everything is P1, nothing is. Reserve P1 for "users are affected right now."

**What to alert on:** **SLO breach**—your error budget is exhausted. **Error rate spike**—sudden increase. **Latency P99**—tail latency matters. **Disk full**—or approaching. **Queue depth**—messages backing up. **Service down**—health check failed. **Capacity**—approaching limits. Each should have a **runbook**—what to do when this fires. No runbook? Don't page. Create a ticket instead.

**What NOT to alert on:** **Individual errors**—one 500 is noise. Alert on rate. **Expected spikes**—Black Friday. Planned maintenance. **Non-actionable**—"something might be wrong." If you can't act, don't alert. **Redundant**—10 alerts for the same root cause. Consolidate. **Low-impact**—dev environment. Staging. Unless it blocks production, don't page.

---

## Another Way to See It

Think of a car's dashboard. **Check engine**—actionable. Pull over. Get it checked. **Low fuel**—actionable. Get gas. **Door ajar**—actionable. Close the door. Now imagine your car alerted you for: "tire pressure 1% low." "Windshield has a speck." "Radio signal weak." You'd ignore the dashboard. When the real warning came, you'd miss it. Alerting is the same. Only show what demands action. Everything else is noise.

---

## Connecting to Software

- **Alert fatigue** = too many alerts = ignore all. Fix: fewer, better, actionable.
- **Actionable** = if you can't act on it, don't alert. Create a ticket instead.
- **Severity:** P1 (page), P2 (hours), P3 (next day), P4 (informational). P1 should be rare.
- **Alert on:** SLO breach, error rate spike, latency P99, disk full, queue depth, service down.
- **Don't alert on:** Individual errors, expected spikes, non-actionable, redundant, low-impact.
- **Runbooks** = link every alert to "what to do." No runbook? Don't page.
- **Real-world:** Google SRE alerting philosophy, PagerDuty best practices. "Every alert must be actionable."

---

## Let's Walk Through the Diagram

```
    ALERTING PYRAMID: WHAT DESERVES A PAGE?

                    ┌─────────┐
                    │   P1   │  PAGE: System down. Revenue impact. Wake someone.
                    │  PAGE  │  Rare. Actionable. Runbook ready.
                    └─────────┘
                   ┌───────────┐
                   │    P2     │  RESPOND: Degraded. Hours. Not critical.
                   │  RESPOND  │  Still actionable. Runbook.
                   └───────────┘
                  ┌─────────────┐
                  │     P3      │  TICKET: Fix next business day.
                  │   TICKET    │  Low urgency. Track it.
                  └─────────────┘
                 ┌───────────────┐
                 │      P4       │  INFORMATIONAL: Log it. No action.
                 │  INFORMATIONAL│  Dashboards. Metrics. Not alerts.
                 └───────────────┘

    KEY: Most things are P3/P4. P1 is the exception. Reserve pages for real emergencies.
```

---

## Real-World Examples (2-3)

**1. Google SRE.** Their philosophy: "Every alert must be actionable." If an engineer can't do something useful when paged, the alert is wrong. They've reduced pages per on-call engineer dramatically. Fewer alerts. Better sleep. Faster response when it matters. The SRE book has a whole chapter on alerting. It's foundational.

**2. PagerDuty.** Built for incident response. They advocate: severity levels, runbooks, escalation policies. "Alert on symptoms, not causes." If the database is slow, alert on "API latency high"—the symptom users feel. Don't alert on "database slow" unless that's what the on-call can fix. Actionable. User-impact focused.

**3. Netflix.** Chaos engineering aside, they're strict about alerting. They've talked about reducing alert volume. Consolidating. Making every page count. The goal: when the pager goes off, it's real. Engineers respond. They don't ignore it.

---

## Let's Think Together

**Question:** Your error rate is 0.1%. Normal. Suddenly it spikes to 1%. Is that a P1? When do you page?

**Pause. Think about it...**

**Answer:** It depends. 1% might be 10 errors or 10,000. Context matters. **SLO-based:** If your SLO is 99.9% uptime, 1% errors for 5 minutes might burn your error budget. Page. **Trend:** Is it rising? Stable? Falling? **Impact:** Are users affected? Payment flow? Login? If 1% of logins fail, that's bad. If 1% of analytics events fail, maybe P2. The rule: page when **user impact** is real and **action** is needed now. Otherwise, ticket. Triage. Don't default to P1.

---

## What Could Go Wrong? (Mini Disaster Story)

A company had 200 alerts. Most fired at least once a day. On-call engineers got 30+ pages per shift. They stopped responding. "Probably fine." One night, the payment service went down. Real outage. Users couldn't checkout. The alert fired. It was alert #12 that hour. The on-call engineer was asleep. They'd muted their phone. The outage lasted 4 hours. Revenue lost. The fix? Alert review. They cut 200 alerts to 15. Each had a runbook. Each was actionable. Pages dropped to 2–3 per week. When the next incident happened, someone responded. The lesson: **alert fatigue kills response**. Fewer alerts. Better alerts.

---

## Surprising Truth / Fun Fact

**Google's SRE team** reportedly had a rule: each on-call engineer should get at most **two pages per week**. If you're paged more, the alerting is broken. Not the engineer. The system. They'd do "alert audits"—review every alert. "Would we page for this at 3 AM?" If not, downgrade or remove. The result: when the pager goes off, it matters. Engineers trust it. They respond. The bar for "page someone" is high. And that's by design.

---

## Quick Recap (5 bullets)

- **Alert fatigue** = too many alerts = ignore all. Fix: fewer, actionable alerts.
- **Actionable** = if you can't act, don't alert. Create a ticket. Link runbooks.
- **Severity:** P1 (page), P2 (hours), P3 (ticket). P1 should be rare.
- **Alert on:** SLO breach, error rate spike, latency, disk, queue, service down. User impact.
- **Don't alert on:** Individual errors, expected spikes, non-actionable, redundant.

---

## One-Liner to Remember

> **Alert only when you can act. Every page needs a runbook. If you can't do something useful, don't wake someone up.**

---

## Next Video

Your services generate data. Your databases store it. But what about transforming raw data into something useful? Loading it into a warehouse? That's **ETL**—the backbone of data pipelines. That's next.
