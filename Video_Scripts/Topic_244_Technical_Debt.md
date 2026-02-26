# Managing Technical Debt at Staff Level

## Video Length: ~4-5 minutes | Level: Senior-Staff

---

## The Hook (20-30 seconds)

You shipped fast. You hit the deadline. You took shortcuts. Copy-pasted code. Skipped tests. Used a quick fix instead of the right fix. It worked. Until it didn't. Now every new feature takes twice as long. Bugs multiply. New engineers are lost. That's **technical debt**—shortcuts you took now that cost you more later. Like financial debt: you borrowed time. Now you're paying interest. How do you manage it? When do you pay it down? When do you live with it? Let me show you.

---

## The Story

**Technical debt** is the implied cost of rework caused by choosing a quick or limited solution instead of a better approach. You "borrowed" time. You shipped. Now you pay "interest"—slower development, more bugs, harder onboarding. Ward Cunningham coined the term. He said: shipping with shortcuts is like financial debt. A little is OK. Too much crushes you.

**Types of debt:** **Intentional**—you knew it was a shortcut. "We'll refactor later." You made a conscious trade-off. **Accidental**—you didn't know better. Requirements changed. The "right" design became wrong. Or you learned something new. Both matter. Intentional debt is easier to track—you chose it. Accidental debt sneaks up. You discover it when you touch the code.

**Why it accumulates:** Pressure to ship. Deadlines. "We'll fix it next sprint." Next sprint never comes. Changing requirements—the design that fit yesterday doesn't fit today. Knowledge loss—the person who wrote it left. No docs. Rot sets in. The key: debt isn't inherently bad. It's a trade-off. The question is: when does the interest exceed the benefit?

**Quantifying and communicating:** Leadership doesn't care about "messy code." They care about impact. "If we don't fix X, feature velocity drops 30% over the next quarter." "Onboarding a new engineer takes 3 weeks instead of 1." "This bug class will recur until we refactor." Tie debt to business outcomes. **Tech debt ratio**—some teams allocate 20% of sprint to debt. Track it. Report it. Make it visible.

---

## Another Way to See It

Think of a house. You skip fixing the leaky roof. It works. Until the ceiling caves in. Or you patch it. Patch on patch. Eventually, the whole roof needs replacing. Technical debt is that. Small shortcuts compound. The "quick fix" becomes the foundation for the next quick fix. At some point, you need to stop and do it right. Or the house falls down.

---

## Connecting to Software

- **Technical debt** = implied cost of rework from shortcuts. You borrowed time; you pay interest.
- **Types:** Intentional (we knew) vs accidental (we didn't know better, or things changed).
- **Why it accumulates:** Pressure to ship, changing requirements, knowledge loss.
- **Quantify:** Tie to business impact. "Velocity drops 30%." "Onboarding 3x slower." Make it visible.
- **Strategies:** Tech debt sprints, boy scout rule (leave code better than you found it), dedicated % of sprint (e.g., 20%).
- **When to pay vs live with it:** Pay when interest exceeds benefit. Live with it when the cost of fixing > cost of living with it.
- **Real-world:** Amazon, Google allocate engineering time for debt. It's a line item. Not optional.

---

## Let's Walk Through the Diagram

```
    TECHNICAL DEBT: BORROW NOW, PAY LATER

    TIME ──────────────────────────────────────────────────────────►

    Sprint 1        Sprint 2        Sprint 3        Sprint N
    ┌─────────┐     ┌─────────┐     ┌─────────┐     ┌─────────┐
    │ Ship    │     │ Ship    │     │ Ship    │     │ Ship    │
    │ fast    │     │ fast    │     │ fast    │     │ fast    │
    │ (debt)  │     │ (debt)  │     │ (debt)  │     │ (debt)  │
    └────┬────┘     └────┬────┘     └────┬────┘     └────┬────┘
         │               │               │               │
         ▼               ▼               ▼               ▼
    ┌─────────┐     ┌─────────┐     ┌─────────┐     ┌─────────┐
    │ Interest│     │ Interest│     │ Interest│     │ PAYDOWN │
    │ grows   │     │ grows   │     │ CRISIS  │     │ or      │
    │         │     │         │     │ velocity│     │ rewrite │
    └─────────┘     └─────────┘     └─────────┘     └─────────┘

    KEY: Allocate % of sprint to debt. Pay interest before it compounds.
```

---

## Real-World Examples (2-3)

**1. Amazon.** Engineering teams allocate a percentage of capacity to "operational excellence" and debt paydown. It's not optional. It's part of planning. If you only ship features, debt grows. Amazon learned this the hard way—early systems became unmaintainable. Now it's baked in.

**2. Google.** "20% time" historically included maintenance and cleanup. Teams have "fixit" weeks—dedicated sprints for debt. The philosophy: you can't outrun debt. You have to budget for it. Otherwise, you'll pay in crises.

**3. Stripe.** Has spoken about "paying down" API debt. They've deprecated old patterns, migrated customers, and invested in cleaner abstractions. It's a conscious choice—short-term pain for long-term health. Leadership supports it because they've seen the cost of not doing it.

---

## Let's Think Together

**Question:** You have two pieces of debt. One: a messy module that slows every change. Two: an outdated library with a known security issue. Which do you fix first?

**Pause. Think about it...**

**Answer:** The security issue. Technical debt has different "interest rates." Security debt compounds fast—one breach and the cost is catastrophic. Code quality debt slows you down gradually. Prioritize by impact and risk. Security, data loss, compliance—fix first. Then debt that blocks velocity. Then "nice to have" cleanups. Not all debt is equal. Triage.

---

## What Could Go Wrong? (Mini Disaster Story)

A startup shipped features for 18 months. No tests. No docs. Copy-paste everywhere. "We'll refactor when we have time." They never had time. They got funding. Hired 10 engineers. Onboarding took months. New engineers broke things. The "original" engineer became a bottleneck—only they understood the code. They quit. The company spent 6 months rewriting. They almost lost the business. The lesson? Debt has a ceiling. Past a point, you can't pay it down incrementally. You need a rewrite. And rewrites are expensive. Pay interest regularly. Don't let it compound to bankruptcy.

---

## Surprising Truth / Fun Fact

**Ward Cunningham**—the guy who invented the wiki—coined "technical debt" in 1992. He was explaining to a non-technical stakeholder why some code was messy. He said: "Shipping with shortcuts is like financial debt. You can ship faster now, but you'll pay interest later. A little debt speeds development. Too much slows it." The metaphor stuck. Today, entire books and conferences are built on it. One metaphor. One conversation. Changed how we talk about code quality.

---

## Quick Recap (5 bullets)

- **Technical debt** = shortcuts now, cost later. You borrow time; you pay interest.
- **Types:** Intentional (we knew) vs accidental (we didn't know, or things changed).
- **Quantify:** Tie to business impact. "Velocity drops 30%." Make it visible to leadership.
- **Strategies:** Tech debt sprints, boy scout rule, dedicated % of sprint (e.g., 20%).
- **When to pay:** When interest exceeds benefit. Prioritize by risk (security first) and impact.

---

## One-Liner to Remember

> **Technical debt: you borrowed time. Pay interest regularly. Don't let it compound to bankruptcy.**

---

## Next Video

The system broke. Users were affected. Now what? You run a **post-mortem**. Not to blame. To learn. That's next.
