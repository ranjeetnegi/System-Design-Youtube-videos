# RPO and RTO: The Numbers That Define Disaster Recovery

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

Your database just died. The whole region is down. How much data did you lose? How long until you're back online? Two numbers answer that. **RPO**—Recovery Point Objective: how far back in time your last good copy is. Lose 5 minutes of data? An hour? A day? **RTO**—Recovery Time Objective: how long it takes to restore service. Minutes? Hours? Days? These aren't buzzwords. They're the contract you make with your business. Get them wrong, and disaster becomes catastrophe.

---

## The Story

**RPO**—Recovery Point Objective—answers: *how much data can you afford to lose?* Measured in time. If your last backup was 1 hour ago, your RPO is 1 hour. A crash at 2pm? You restore from the 1pm backup. You lost everything between 1pm and 2pm. **RPO = 0** means no data loss. You need **synchronous replication**—every write confirmed in two places before you acknowledge. Expensive. Complex. Banking, payments—often RPO = 0. **RPO = 24 hours**? Daily backup. A blog, internal tool—maybe fine. You accept losing a day. The lower the RPO, the more you spend: frequent backups, sync replication, multi-site writes.

**RTO**—Recovery Time Objective—answers: *how fast do you need to be back online?* Measured in time. RTO = 5 minutes? You need **hot standby**, **automated failover**. The moment primary dies, traffic switches. No manual steps. **RTO = 4 hours**? Maybe you restore from backup. Start the servers. Run the restore script. Manual. Slower. Cheaper. **RTO = 0** (or "instant")? Active-active. Two regions. No single point of failure. Complex. Costly.

**Cost vs risk trade-off.** Lower RPO and RTO = more money, more complexity. Higher = cheaper, but more risk. You calculate based on your business. A bank: RPO near 0, RTO in minutes. A hobby blog: RPO 24h, RTO 24h. Both valid. Different requirements.

---

## Another Way to See It

A photo backup. RPO: how often do you back up? Every hour? Every day? If your phone dies, you lose photos since the last backup. RPO = backup frequency. RTO: how long to get your photos back? Restore from iCloud in 10 minutes? Or ship the phone to a repair shop, wait a week? RTO = recovery speed. Same for systems. Backup frequency = RPO. Recovery procedure = RTO.

---

## Connecting to Software

- **Lower RPO** → more frequent backups (every 5 min, every minute). Or **synchronous replication**—write to primary and replica before ack. **MySQL** semi-sync, **PostgreSQL** sync replication. **RPO = 0** often means sync replication to another region.
- **Lower RTO** → **hot standby** (replica always ready). **Automated failover** (health check, promote replica, update DNS). **Multi-AZ**, **multi-region**. Less manual steps = faster recovery.
- **How to calculate:** Ask the business. "How much data loss is acceptable?" → RPO. "How long can we be down?" → RTO. Then design: backup frequency, replication mode, failover automation.
- **Real tools:** **AWS RDS** Multi-AZ (sync replica, auto-failover). **Aurora** cross-region. **Redis** replication. **Kafka** replicated topics. All tune RPO and RTO.

---

## Let's Walk Through the Diagram

```
    RPO & RTO VISUALIZED

    Timeline:  ──────────────────────────────────────────────▶
               |     |     |     |     |     |     |     |
    Backups:   9am   10am  11am  12pm  CRASH 1pm
               ●     ●     ●     ●     💥    ?
               │     │     │     │           │
               └─────┴─────┴─────┴───────────┘
                     Last good backup = 12pm
                     RPO = 1 hour (lost 12pm–1pm data)

    Recovery:
    CRASH ──────────────────────────────────▶ LIVE
    1pm         restore, failover, DNS      2pm
               └─────────────────────────────┘
                     RTO = 1 hour (downtime)

    LOWER RPO:  More backups (every 15 min)  →  Less data loss
    LOWER RTO:  Hot standby + auto-failover  →  Less downtime
```

**Left:** RPO = how far back your last good copy. **Right:** RTO = how long from crash to live. Both are design targets. You build to meet them.

---

## Real-World Examples (2-3)

**Example 1 — Banking:** RPO near 0. Every transaction must survive. Sync replication. Write to primary and standby. Both confirm before ack. RTO in minutes. Automated failover. Database dies? Standby promotes. DNS switches. Customers might see a blip. Not data loss. Not hours of downtime. Billions depend on it.

**Example 2 — E-commerce product catalog:** RPO = 1 hour. Hourly backups. If the DB dies, you lose up to an hour of product updates. Acceptable. RTO = 2 hours. Restore from backup. Start instances. Update DNS. Manual but fine. Products don't change every second. Cost-effective.

**Example 3 — Internal blog:** RPO = 24 hours. Daily backup. RTO = 24 hours. Restore when you have time. Cheap. Simple. Risk is low. No payments. No critical path. Different systems, different numbers.

---

## Let's Think Together

Your system has RPO = 1 hour (hourly backups) and RTO = 4 hours (manual restore). A compliance audit says: "You must recover within 30 minutes with no more than 5 minutes of data loss." What changes?

Pause and think.

RPO = 5 min → backups every 5 minutes, or sync replication. RTO = 30 min → automated failover, hot standby. No manual restore. You need Multi-AZ, or cross-region replication, automated health checks, DNS failover. More cost. More complexity. But that's what the requirement demands.

---

## What Could Go Wrong? (Mini Disaster Story)

A SaaS company had "daily backups" and "we'll restore when we can." RPO = 24h, RTO = undefined. One Friday, a bug in a deployment script truncated the main table. 40,000 customer records—gone. They restored from the previous night's backup. Lost 18 hours of data. Customers who signed up that day? Vanished. Orders placed? Lost. RTO was 6 hours—restore took time. But RPO was the killer. 18 hours of data. No sync replication. No point-in-time recovery. They'd never calculated RPO. They learned the hard way. Lesson: know your RPO and RTO before disaster. Design for them. Or accept the consequences.

---

## Surprising Truth / Fun Fact

Many companies discover their RPO and RTO during the disaster. "How far back can we restore?" "Uh... last backup was 3 days ago." "How long to get back up?" "We're still figuring it out." Don't be that company. Calculate upfront. Test restore. Run failover drills. The numbers aren't theoretical. They're your survival plan.

---

## Quick Recap (5 bullets)

- **RPO** (Recovery Point Objective) = how much data you can afford to lose. Measured in time. Lower = more backups or sync replication.
- **RTO** (Recovery Time Objective) = how fast you need to be back online. Lower = hot standby, automated failover.
- **Cost vs risk:** Lower RPO/RTO = more money, more complexity. Higher = cheaper, riskier.
- **Calculate for your system:** Ask the business. Design backups, replication, failover to meet the numbers.
- **Real-world range:** Banking (RPO≈0, RTO=minutes). Blog (RPO=24h, RTO=hours). Match the system to the need.

---

## One-Liner to Remember

> RPO: how much data can you lose? RTO: how long can you be down? Know both. Design for both. Before disaster does the math for you.

---

## Next Video

Your app runs in a container. One container is easy. But what about 100 containers on 50 machines? Who restarts crashed ones? Who scales them up? That's **Kubernetes**. That's next.
