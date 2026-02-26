# How to Practice System Design (Action Plan)

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

You've learned the concepts. Load balancers, caching, sharding. But when someone says "design a URL shortener," your mind goes blank. Knowing *about* system design isn't the same as *doing* system design. It's like reading about swimming versus jumping in the pool. You need a practice plan. Where do you start? How do you practice without an interviewer? Here's your action plan.

---

## The Story

**Where to start:** Pick one problem. URL shortener. Rate limiter. Chat system. Don't overthink it. Set a timer for 45 minutes. Write down requirements. Draw a diagram. Identify bottlenecks. That's one session. Do it again tomorrow with a different problem. Consistency beats intensity. Twenty problems over twenty days beats cramming ten in one weekend.

**How to practice without an interviewer:** You don't need someone across the table. Design alone. Talk out loud—record yourself. Explain your choices. "I'm using a cache here because..." The act of verbalizing forces clarity. Or write a **design diary**: document your thinking in a doc. Requirements. Options considered. Trade-offs. Final design. Why you chose X over Y. That document becomes your review material. You'll spot gaps when you read it back.

**Mock interviews:** Once you're comfortable alone, add pressure. A friend. A peer. A paid mock service. The interviewer adds something you can't simulate: ambiguity. "Scale to 10 million users." "What if the database goes down?" Real interviews throw curveballs. Mocks prepare you for that. Do at least five before your real interview.

**Reading real engineering blogs:** Uber's ride-matching. Netflix's encoding pipeline. Meta's feed ranking. These aren't toy problems. They're production systems with real constraints. Read them. Notice the patterns. How do they handle scale? Failure? Consistency? Engineering blogs are free design lessons from people who built it.

---

## Another Way to See It

Learning system design is like learning chess. You study openings. You memorize endgames. But you get better by *playing games*. Each design problem is a game. You make moves. You see what works. You lose. You learn. The practice framework is your board: pick problem, set timer, write requirements, draw diagram, identify bottlenecks, check against published solutions. Play the game. Repeat.

---

## Connecting to Software

- **Practice framework:** Pick problem → Set timer (45 min) → Write requirements (functional + non-functional) → Draw high-level diagram → Identify bottlenecks → Propose solutions → Check against published solutions (e.g., ByteByteGo, System Design Interview books).
- **Design diary:** Document your thinking. Use tools like Notion, Obsidian, or plain markdown. Structure: Problem, Requirements, Options, Trade-offs, Final Design, Lessons.
- **Resources:** Engineering blogs—**Uber Engineering**, **Netflix Tech Blog**, **Meta Engineering**, **Stripe**, **Airbnb**. Also: **ByteByteGo**, **System Design Primer** (GitHub), **Designing Data-Intensive Applications**.
- **Mock platforms:** Pramp, Interviewing.io, Exponent. Or find a study buddy. The key is external pressure and feedback.
- **Common mistakes:** Skipping requirements. Jumping to solutions. Not drawing. Not talking. Practice the *process*—not just the answer.

---

## Let's Walk Through the Diagram

```
PRACTICE FRAMEWORK (One Session)
===============================

1. PICK PROBLEM (2 min)
   URL shortener | Rate limiter | Chat | Feed

2. REQUIREMENTS (5 min)
   Functional: shorten, redirect, analytics?
   Non-functional: QPS, latency, durability?

3. HIGH-LEVEL DIAGRAM (15 min)
   Client → API → ? → Storage
   Identify: API, DB, cache, CDN

4. BOTTLENECKS (10 min)
   - DB bottleneck? → Sharding, caching
   - Redirect latency? → CDN, edge
   - Write throughput? → Async, batching

5. DEEP DIVE (10 min)
   Pick ONE hardest component. Go deep.

6. CHECK (3 min)
   Compare to published solution. What did you miss?

   Total: ~45 min. Repeat. Different problem each time.
```

---

## Real-World Examples (2-3)

**1. Uber Engineering Blog:** Their ride-matching system. Geospatial indexing. Real-time location. Surge pricing. Read it. Notice how they handle "find nearby drivers" at scale. That's a design problem you might get. Their solution is production-tested.

**2. Netflix Tech Blog:** Encoding pipeline. How they transcode video for different devices. Adaptive bitrate. CDN strategy. Real constraints: storage costs, bandwidth, latency. You learn from their trade-offs.

**3. Meta Engineering:** Feed ranking. Real-time signals. Batch signals. Cold start. Scale. These blogs are gold. Free. Written by people who built it. Read one per week. Take notes. Connect to problems you've practiced.

---

## Let's Think Together

**Question:** You're practicing alone. No interviewer. You have 45 minutes. Do you: (A) Pick a problem you've never seen, or (B) Redo a problem you've already designed?

**Pause. Think about it...**

**Answer:** **Both.** Alternate. New problems: you build the skill of tackling the unknown. Redo problems: you go deeper. You'll notice things you missed. "Last time I didn't consider caching. This time I will." Redoing improves quality. New problems improve breadth. Do both. One session per day. New, redo, new, redo... You'll build a library of designs and deepen your thinking.

---

## What Could Go Wrong? (Mini Disaster Story)

A candidate practiced for weeks. They designed twenty systems. Alone. They never talked out loud. Never did a mock. They walked into the interview. "Design a URL shortener." They knew the answer. They drew the diagram. But they froze when explaining. "Why a cache?" "Why not a database?" They couldn't articulate. They knew the design but couldn't *defend* it. The interviewer probed. They stumbled. They didn't get the offer. The lesson: practicing alone is necessary but not sufficient. You must verbalize. You must defend. Do mocks. Record yourself. The skill isn't just knowing—it's communicating under pressure.

---

## Surprising Truth / Fun Fact

Many top engineers at FAANG companies have *never* formally studied system design. They learned by building. By reading code. By debugging production. By reading engineering blogs. The interview tests a *compressed* version of that experience. Your practice is simulating years of building in 45 minutes. That's why the framework matters: requirements, diagram, bottlenecks, trade-offs. It's a structured way to show what you'd figure out over time. The interview is a shortcut. Practice is the accelerator.

---

## Quick Recap (5 bullets)

- **Practice framework:** Pick problem → Set timer → Requirements → Diagram → Bottlenecks → Check against solutions.
- **Design diary:** Document your thinking. Requirements, options, trade-offs. Review later. Spot gaps.
- **Mock interviews:** Add pressure. External feedback. Ambiguity. Do at least five before the real interview.
- **Engineering blogs:** Uber, Netflix, Meta. Real systems. Real constraints. Free lessons. Read one per week.
- **Verbalize:** Practice alone is good. But talk out loud. Record. Defend. The skill is communicating under pressure.

---

## One-Liner to Remember

> **Knowing concepts isn't enough. Practice the process: pick problem, set timer, write requirements, draw diagram, identify bottlenecks. Repeat. Add mocks. Read real blogs.**

---

## Next Video

You have the mindset. You have the framework. Now let's put everything into practice with real design problems. First up: **designing a unique ID generator**—requirements, options (UUID, database, Snowflake), trade-offs, and component design. The classic interview problem.
