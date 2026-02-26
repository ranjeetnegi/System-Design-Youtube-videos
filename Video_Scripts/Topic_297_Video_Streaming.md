# Design Problem: Video Streaming Platform

## Video Length: ~4-5 minutes | Level: Senior-Staff

---

## The Hook (20-30 seconds)

You upload a 4K video. Ten minutes later, it's streaming on phones, tablets, TVs. Different resolutions. Different bitrates. Buffering adapts to your connection. How? Upload pipeline. Transcoding. CDN. Adaptive bitrate. It's a multi-stage system. YouTube, Netflix, Vimeo—they all solve this. Let's design it.

---

## The Story

**Requirements.** **Upload**—large files, resumable, reliable. **Transcode**—convert to multiple formats and resolutions. 4K, 1080p, 720p, 480p. Different codecs (H.264, VP9, AV1). **Stream**—low latency, smooth playback. **Adaptive bitrate**—network fluctuates. Switch quality on the fly. No buffering. **Scale**—millions of videos, billions of views.

**Upload pipeline.** User uploads. **Chunked upload**—split into parts. Resumable. If connection drops, resume from last chunk. **Pre-signed URLs**—client uploads directly to **S3** or **GCS**. Backend doesn't handle bytes. Saves bandwidth. Upload completes → trigger **transcoding job**. Async. Don't block the user.

**Transcoding.** Raw upload is one format. You need many. **FFmpeg** is the workhorse. Input: 4K source. Output: 1080p, 720p, 480p, 360p. Each resolution, multiple bitrates (e.g., 1080p at 5 Mbps, 3 Mbps, 1.5 Mbps). **Adaptive Bitrate Streaming (ABR)** requires these variants. **HLS** (Apple) or **DASH** (open) format—manifest + segments. Each segment is 2-10 seconds of video. Player fetches segments. Chooses quality based on bandwidth. Transcoding is **CPU-intensive**. Use a **worker pool** or **serverless** (Lambda, Cloud Run). Queue jobs. Scale workers.

**CDN for delivery.** Don't serve video from origin. **CDN**—CloudFront, Cloudflare, Fastly. Cache segments at edge. User in Tokyo gets segments from Tokyo edge. Low latency. High throughput. Video is **cache-friendly**—segments are immutable. Cache forever (or long TTL). Origin serves transcoded output. CDN caches it. 99% of requests hit CDN.

**Adaptive Bitrate (ABR).** Player has a **manifest**—list of segments at each quality level. Player measures bandwidth. Requests next segment at appropriate quality. Fast connection → 1080p. Slow → 480p. Buffering? Drop to lower quality. **HLS** and **DASH** are the standards. Player logic: "I have 5 seconds buffered. Last segment took 2 seconds. Request 720p next." Smooth experience.

---

## Another Way to See It

A restaurant. Raw ingredients (upload). Kitchen prepares multiple dishes (transcode)—spicy, mild, vegetarian. Same base, different outputs. Delivery (CDN)—food goes to locations near customers. Not one central kitchen. Customer picks what they can handle (adaptive)—spicy if they like it, mild if not. Video streaming is that: prepare variants, distribute to edge, let player choose.

---

## Connecting to Software

- **Upload:** **Chunked upload** (multipart). **Pre-signed URLs** for direct-to-S3. **Resumable** (checkpoint).
- **Transcoding:** **FFmpeg**, **AWS MediaConvert**, **GCP Transcoder API**. Output: HLS/DASH segments. Multiple resolutions, bitrates.
- **Job queue:** **SQS**, **RabbitMQ**, **Kafka**. Upload complete → enqueue transcode job. Workers process.
- **CDN:** **CloudFront**, **Cloudflare**, **Fastly**. Cache segments. Edge locations. Low latency.
- **ABR formats:** **HLS** (m3u8 manifest, .ts segments), **DASH** (MPD manifest, .m4s segments). **Player:** Video.js, ExoPlayer, AVPlayer.
- **Real tools:** **YouTube** (proprietary), **Netflix** (Open Connect CDN), **Mux**, **Cloudflare Stream**.

---

## Let's Walk Through the Diagram

```
VIDEO STREAMING PIPELINE
========================

Upload                  Transcode                 Delivery
------                  ---------                 --------

User --chunked--> API --presigned URL--> S3
  |                    |
  |                    v
  |              [Upload Complete]
  |                    |
  |                    v
  |              Job Queue (SQS)
  |                    |
  |                    v
  |              Worker (FFmpeg)
  |                    |
  |              Input: 4K source
  |              Output: 1080p, 720p, 480p (HLS segments)
  |                    |
  |                    v
  |              S3 (transcoded output)
  |                    |
  |                    v
  +---------------> CDN (CloudFront)
                         |
                         v
                    Edge locations
                         |
                         v
                    User (player)
                    - Fetches manifest
                    - Chooses quality (ABR)
                    - Fetches segments
                    - Plays
```

---

## Real-World Examples (2-3)

**1. YouTube:** Massive scale. Upload to Google infrastructure. Transcoding pipeline—many formats, many devices. CDN (Google's global network). ABR. They've written about their pipeline. Handles 500+ hours of upload per minute.

**2. Netflix:** Open Connect—their own CDN. ISPs host Netflix servers. Video cached at ISP edge. Ultra-low latency. Transcoding: many profiles (device, connection). Per-title encoding—optimize per movie. Not one-size-fits-all.

**3. Mux (API):** Video infrastructure as a service. Upload, transcode, stream. They handle the pipeline. You integrate.

---

## Let's Think Together

**Question:** Transcode on upload or on first view?

**Pause. Think about it...**

**Answer:** **On upload** is standard. User uploads. You transcode. Video is ready when anyone watches. Pro: first view is fast. Con: you transcode everything. Many videos get zero views. Wasted compute. **On first view** (lazy): transcode when first user requests. Pro: save compute on unpopular videos. Con: first viewer waits. Bad UX. **Hybrid:** Transcode popular formats on upload (720p, 1080p). Transcode 4K or niche codecs on first view. Balance cost and UX. Most platforms: transcode on upload. Cost of compute < cost of bad first-view experience.

---

## What Could Go Wrong? (Mini Disaster Story)

A streaming startup transcoded on first view. Viral video. 10 million views in an hour. First 1000 viewers triggered 1000 transcode jobs. Same video. No deduplication. Queue backed up. Transcode took 10 minutes per video. Users saw "Processing..." for 10 minutes. Rage. The fix: **deduplicate transcode jobs**. One job per video. Others wait. And they moved to **transcode on upload** for expected viral content. Lesson: transcode is the bottleneck. Don't let N viewers trigger N jobs for the same video. Deduplicate. Queue. One job per video.

---

## Surprising Truth / Fun Fact

Netflix uses **per-title encoding**. Not fixed bitrate ladder. They analyze each movie. Action movie: more bits for fast scenes. Drama: fewer bits, more compression. Same quality, 20-30% bandwidth savings. Encoding is not one-size-fits-all. Content-aware encoding is the future. FFmpeg can do it. So can commercial encoders. Mention it in design—shows depth.

---

## Quick Recap (5 bullets)

- **Upload:** Chunked, resumable. Pre-signed URLs. Direct to S3. Trigger transcode on complete.
- **Transcode:** FFmpeg. Multiple resolutions, bitrates. HLS/DASH output. Job queue. Worker pool.
- **CDN:** Cache segments at edge. Immutable. Long TTL. 99% cache hit. Low latency.
- **ABR:** Manifest + segments. Player chooses quality by bandwidth. HLS, DASH. Smooth playback.
- **Scale:** Async pipeline. Deduplicate transcode jobs. Transcode on upload for UX.

---

## One-Liner to Remember

> **Upload → Transcode (FFmpeg, multiple formats) → Store → CDN → Player (ABR). Chunked upload, job queue, edge delivery.**

---

## Next Video

Video streaming done. Next: **ride-sharing service**. Match rider and driver. Real-time location. ETA. Pricing. Geospatial indexing. Uber, Lyft scale. How do you build it? Let's design it.
