# Database Backup and Recovery: Don't Lose Your Data

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

Your database runs. Queries are fast. Users are happy. Then: a bad migration. A disgruntled employee. A ransomware attack. A hard drive failure. The data is gone. What do you do? Backups. You've heard it a million times. "Have backups." But what kind? Where? How often? And when was the last time you actually restored one? A backup you haven't tested is not a backup. Let's get this right.

---

## The Story

**Why backups matter:** Hardware fails. Disks die. Humans run `DROP TABLE` by mistake. Ransomware encrypts everything. Natural disasters. The question isn't "will something go wrong?" It's "when?" Backups are your insurance. If you can't restore, you're not insured.

**Backup types.** **Full backup:** Copy everything. Every table, every row. Simple. Complete. But slow and large. A 1TB database takes hours to backup and hours to restore. **Incremental backup:** Only what changed since the last backup. Smaller. Faster. But restore requires the full backup plus every incremental. Chain of dependencies. **Differential backup:** What changed since the last *full* backup. Simpler than incremental—restore = full + latest differential. Trade-off: differentials grow over time.

**Point-in-time recovery (PITR):** You have a full backup from 2am. A disaster at 2:45pm. Can you restore to 2:44pm? Yes—with **WAL** (PostgreSQL) or **binlog** (MySQL). The database writes every change to a log. Backup + replay the log up to a timestamp. You recover to the exact second before the disaster. **PITR** is the difference between "we lost 12 hours of data" and "we lost 0."

**Where to store backups:** Not on the same server. Not in the same region. **S3**, **GCS**, **Azure Blob**—different region, different availability zone. Ransomware that encrypts your primary might encrypt your backups if they're on the same network. **3-2-1 rule:** 3 copies, 2 different media types, 1 offsite.

**Testing backups:** A backup you haven't tested is not a backup. Run a restore. In a staging environment. Every quarter. Every month. Prove it works. Document the steps. When disaster strikes, you don't want to be reading the manual for the first time.

---

## Another Way to See It

Backups are like a spare tire. You hope you never need it. But when you have a flat, you need to know it's there, it's inflated, and you know how to put it on. A spare tire you've never checked? Might be flat. A backup you've never restored? Might be corrupt. Test it.

---

## Connecting to Software

- **Full backup:** `pg_dump` (PostgreSQL), `mysqldump` (MySQL). Complete snapshot. Slow for large DBs.
- **Incremental:** `pg_basebackup` + WAL archiving (PostgreSQL). Only changed blocks. Faster.
- **PITR:** PostgreSQL WAL, MySQL binlog. Replay to a timestamp. Restore to the second before disaster.
- **Where to store:** **AWS S3**, **GCS**, **Azure Blob**. Different region. Cross-region replication for critical data.
- **RTO (Recovery Time Objective):** How fast must you recover? 1 hour? 24 hours? Drives backup strategy.
- **RPO (Recovery Point Objective):** How much data can you afford to lose? 1 hour? 1 day? Drives backup frequency.
- **Managed services:** **RDS**, **Cloud SQL**—automated backups, PITR. Configure retention. Test restore.

---

## Let's Walk Through the Diagram

```
BACKUP STRATEGY:

  Full Backup (Sunday 2am)     Incremental Backup (daily)    WAL/binlog (continuous)
  |                           |                           |
  v                           v                           v
  +----------------+          +----------------+          +----------------+
  |  Complete DB   |          |  Changes only  |          |  Every write   |
  |  snapshot      |          |  since last    |          |  logged        |
  +----------------+          +----------------+          +----------------+
         |                            |                            |
         |                            |                            |
         +----------------------------+----------------------------+
                                      |
                                      v
  RESTORE: Full + Incrementals + Replay WAL to timestamp = PITR
                                      |
                                      v
  Database restored to exact second before disaster
```

---

## Real-World Examples (2-3)

**1. GitLab 2017:** A developer accidentally ran `rm -rf` on the production database. The primary and one replica were wiped. They had backups—but the restore process was slow, and some backup jobs had failed silently. They lost 6 hours of data (issues, merge requests, comments). They had to restore from 6 hours prior. Lesson: test backups. Monitor backup jobs. Know your restore procedure. GitLab published a post-mortem. They're now known for their backup discipline.

**2. AWS RDS:** Automated backups. Retention: 7 days. Point-in-time recovery: restore to any second in that window. One click. The backup is in a different region. Ransomware hits your primary? Restore from backup. Managed services handle the complexity.

**3. PostgreSQL in production:** `pg_dump` for full. `pg_basebackup` + WAL archiving for continuous. Store WAL in S3. `restore_command` replays WAL. Restore to the second. Open-source. Battle-tested. Used by companies of all sizes.

---

## Let's Think Together

**Question:** Your database is 500GB. Full backup takes 4 hours. You run it nightly. A disaster happens at 11am. How much data do you lose?

**Pause. Think about it...**

**Answer:** Up to 11 hours—from the last backup at midnight to 11am. If you need to lose less, you need more frequent backups or **continuous WAL/binlog archiving**. With PITR: backup + replay WAL to 10:59:59am. You lose nothing. Or minimal. The trade-off: backup frequency and storage for WAL logs. For critical systems, PITR is non-negotiable.

---

## What Could Go Wrong? (Mini Disaster Story)

A company had nightly backups to S3. They never tested. One day: ransomware. Primary encrypted. They went to restore. The backup was there. But the restore script assumed a specific S3 path. They'd changed the bucket structure 6 months ago. The script failed. No one had run it since. They had to manually fix the script, figure out the new paths, and restore. 12 hours of downtime. The backup existed. The process didn't. Test your restore. Every quarter. Document it. Run it. A backup is only as good as your ability to restore it.

---

## Surprising Truth / Fun Fact

**GitLab's 2017 incident** led them to create a "disaster recovery" runbook that's now public. They practice restoring from backups. They have a "break things" culture for testing. They've turned a disaster into a strength. Their backup and restore docs are now a reference for the industry. Failure taught them. They shared the lesson.

---

## Quick Recap (5 bullets)

- **Full backup:** Everything. Complete. Slow. Run periodically.
- **Incremental/Differential:** Only changes. Faster. Restore = full + incrementals.
- **PITR:** WAL/binlog. Replay to a timestamp. Restore to the second before disaster.
- **Where to store:** Different region. S3, GCS. 3-2-1 rule.
- **Test:** A backup you haven't restored is not a backup. Test quarterly. Document. Practice.

---

## One-Liner to Remember

> Backups are insurance. Full + incremental + PITR. Store offsite. Test the restore. A backup you never tested is a hope, not a plan.

---

## Next Video

Backups help you recover. But how fast do you need to recover? And how much data can you afford to lose? That's **RPO and RTO**—the numbers that define disaster recovery. That's next.
