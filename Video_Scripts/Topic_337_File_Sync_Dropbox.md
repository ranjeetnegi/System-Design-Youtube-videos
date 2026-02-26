# Design Problem: File Sync Service (Dropbox)

## Video Length: ~4-5 minutes | Level: Senior-Staff

---

## The Hook (20-30 seconds)

You edit a file on your laptop. Save. A second later, it's on your phone. You're offline. Edit again. Go online. Sync. Both versions exist. Which wins? A file sync service isn't just "upload and download." It's **chunking**, **deduplication**, **conflict resolution**, and **notification** across devices. Dropbox, Google Drive, OneDrive—they all solve this. Let's design it.

---

## The Story

**Requirements.** **Sync across devices**—change on one, reflect on all. **Offline support**—edit without internet. Sync when connected. **Conflict resolution**—two devices edit the same file offline. What do you do? **Efficient transfer**—don't re-upload the whole file if one byte changed. **Scale**—millions of users, billions of files.

**Chunking large files.** Split files into **chunks** (e.g., 4MB). Each chunk has a **hash** (SHA-256). Upload chunks. If you edit one part of a 1GB file, only changed chunks re-upload. **Deduplication**—same chunk across users? Store once. "I have the same PDF as you." One copy. Huge storage savings. Chunking enables **delta sync**—only send what changed.

**Metadata service.** A **metadata DB** stores: file path, chunk IDs, version, last modified, user. Not the file content—just the manifest. "Document.pdf = [chunk_A, chunk_B, chunk_C]." Client asks: "What's the latest version of /docs/report.pdf?" Metadata service responds. Client compares local chunks to remote. Downloads missing chunks. Uploads new chunks. Metadata is the source of truth for structure. Content lives in **object storage** (S3, GCS).

**Notification service.** How does the client know something changed? **Long polling** or **WebSocket**. Client: "Any updates?" Server: "Yes. File X changed." Client fetches metadata. Syncs. **WebSocket** is push—instant. **Long polling** is simpler—client polls every 30s. Trade-off: latency vs complexity. Dropbox has used both. The key: client must be notified. Can't rely on client polling metadata constantly—too expensive.

**Conflict resolution.** Two devices edit offline. Both sync. **Last-write-wins**—simple but loses one version. **Merge**—for text, diff and merge. For binary? Hard. **Keep both**—"report_conflict.pdf" and "report_conflict (1).pdf." User decides. Most systems: last-write-wins for simplicity, with "version history" to recover. Advanced: operational transform, CRDTs for real-time collaboration. That's another level.

---

## Another Way to See It

A library. Books are chunks. The catalog is metadata—"Shelf 3, Book 2, Book 5." You don't copy the whole library. You check the catalog. "I need Book 2." You fetch it. Someone adds a new book. The catalog updates. You get a notice: "Catalog updated." You check. You fetch what's new. File sync is that—catalog (metadata) + storage (chunks) + notification (catalog changed).

---

## Connecting to Software

- **Chunking:** Split file into fixed-size blocks (4MB common). Hash each (SHA-256). Content-addressable. Deduplication.
- **Metadata service:** **PostgreSQL** or **MySQL**. Schema: files (path, user_id, chunk_ids[], version, modified_at). Indexed by user + path.
- **Object storage:** **S3**, **GCS**, **Azure Blob**. Chunk storage. Cheap. Durable. Key = chunk hash.
- **Notification:** **WebSocket** (push), **long polling** (poll every 30s), **Server-Sent Events**. Client subscribes. Server pushes "metadata changed."
- **Conflict resolution:** Last-write-wins + version history. Or keep both. Merge for text (complex).
- **Real tools:** **Dropbox** (block sync, custom protocol), **Google Drive**, **rsync** (delta sync algorithm), **SyncThing** (open source).

---

## Let's Walk Through the Diagram

```
FILE SYNC ARCHITECTURE
======================

Client A                Metadata Service           Object Storage (S3)
   |                            |                          |
   |-- Get file manifest ------>|                          |
   |<-- [chunk_A, chunk_B] ------|                          |
   |                            |                          |
   |-- Fetch chunk_A -------------------------------->     |
   |<----------------------------------------------- chunk_A|
   |-- Fetch chunk_B -------------------------------->     |
   |<----------------------------------------------- chunk_B|
   |                            |                          |
   | (User edits file)          |                          |
   |-- Upload new chunk_C -------------------------->     |
   |                            |                          |
   |-- Update manifest -------->| (chunk_A,B,C)            |
   |                            |-- Notify Client B ------>|
   |                            |                          |

Client B (WebSocket): "Metadata changed for /docs/report.pdf"
  → Fetches new manifest
  → Sees chunk_C is new
  → Downloads chunk_C
  → Reconstructs file
```

---

## Real-World Examples (2-3)

**1. Dropbox:** Pioneered block-level sync. 4MB chunks. Content-addressable. Deduplication across users. They've published on their architecture. Metadata in MySQL. Blocks in S3-like storage. Notification via long polling (historically), now more real-time.

**2. Google Drive:** Similar. Chunking. Metadata. Collaboration adds complexity—multiple editors, real-time. Operational transform or CRDTs for Google Docs. For regular files, simpler sync model.

**3. SyncThing (open source):** P2P sync. No central server. Devices discover each other. Chunk-based. Block exchange. Good reference for decentralized sync.

---

## Let's Think Together

**Question:** Chunk size: 1MB or 8MB? Trade-off?

**Pause. Think about it...**

**Answer:** **4MB is a common sweet spot.** Smaller (1MB): finer granularity. Edit 1 byte, re-upload 1 chunk. Less redundant transfer. But more chunks = more metadata = more overhead. Larger (8MB): fewer chunks. Less metadata. But edit 1 byte, re-upload 8MB. More redundant transfer. 4MB balances. Dropbox uses ~4MB. Also consider: **chunk boundary problem**. Insert 1 byte at start of file. All chunks shift. Content changes. Many chunks "change" even though content is similar. **Content-defined chunking** (e.g., rolling hash) creates variable-size chunks that don't shift. More complex. Used in rsync, some enterprise sync. For interview: 4MB fixed is fine. Mention content-defined as advanced optimization.

---

## What Could Go Wrong? (Mini Disaster Story)

A sync product had a bug in conflict resolution. User edited a file on laptop. Offline. Edited same file on phone. Offline. Both came online. Last-write-wins. Phone version won. Laptop version—gone. No version history. User had written a 50-page report on laptop. Lost. They had to restore from backup. The fix: they added **version history**. Keep last N versions. Allow restore. And they improved conflict UX: "Both versions exist. Here's yours. Here's the other. Choose or merge." Lesson: last-write-wins is dangerous without version history. Always keep backups. And consider "keep both" as default for conflicts—let user decide.

---

## Surprising Truth / Fun Fact

Dropbox's early deduplication saved ~25% storage. Many users store the same files—common PDFs, installers, memes. Content-addressable chunks mean one copy serves many. At scale, that's massive savings. The trade-off: **privacy**. If chunks are shared, does the provider "see" that two users have the same file? Encryption complicates—encrypt per user, can't dedupe across users. Encrypt per chunk, can dedupe. Trade-off: privacy vs storage efficiency. Real systems balance both.

---

## Quick Recap (5 bullets)

- **Chunking:** Split files. Hash chunks. Delta sync. Only transfer what changed. Deduplication.
- **Metadata service:** File manifest (path, chunks, version). Source of truth. Stored in DB.
- **Object storage:** Chunks in S3/GCS. Key = hash. Cheap. Durable.
- **Notification:** WebSocket or long polling. Client notified when metadata changes. Triggers sync.
- **Conflict resolution:** Last-write-wins + version history. Or keep both. Merge for text.

---

## One-Liner to Remember

> **Chunk, hash, store. Metadata for structure. Object storage for content. Notify on change. Resolve conflicts—last-write-wins or keep both.**

---

## Next Video

File sync done. Next: **video streaming platform**. Upload. Transcode. Stream. Adaptive bitrate. YouTube, Netflix scale. How do you build it? Upload pipeline. FFmpeg. CDN. HLS/DASH. Let's design it.
