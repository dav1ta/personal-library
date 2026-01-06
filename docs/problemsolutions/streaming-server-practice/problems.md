# Problem 1: Zero-Copy Protocol Parsing (Performance)

**Real-World Scenario:** 
A naive server reads 1 byte at a time from the socket (`read(1)`) to parse headers. With 6Mbps streams, this causes millions of system calls and context switches, destroying CPU performance. Python's GC also chokes on creating millions of tiny `Packet` objects.

**System Design Pattern:** 
Buffered I/O & Memory Pooling (Slab Allocation)

**Solution Logic:** 
We will implement a **User-Space Read Buffer**. We read 16KB+ chunks from the kernel socket into a pre-allocated `bytearray`. The parser uses `memoryview` to slice headers and payloads without copying data. We recycle these buffers to minimize GC pressure.

# Problem 2: The "GOP Cache" & Instant Playback

**Real-World Scenario:** 
A user joins a stream. If you just send them the *current* packet, it might be a P-frame (inter-frame). The player cannot decode it without the previous I-frame (Keyframe). The screen stays black or artifacted for seconds until the next Keyframe arrives.

**System Design Pattern:** 
Ring Buffer / GOP Caching

**Solution Logic:** 
The server must maintain a **GOP (Group of Pictures) Cache**. It stores the sequence of tags starting from the last Video Keyframe. When a new subscriber (Player or HLS Segmenter) joins, we immediately "fast-forward" them by sending the cached GOP so playback starts instantly.

# Problem 3: One-to-Many Fanout (Pub/Sub)

**Real-World Scenario:** 
One broadcaster (OBS) sends video. You have 3 consumers:
1. HLS Segmenter (for web playback)
2. Archive Worker (save to disk)
3. RTMP Relay (push to YouTube)
If you hardcode the logic, adding consumers is impossible. If one consumer is slow (disk I/O), it blocks the others.

**System Design Pattern:** 
In-Memory Pub/Sub (Observer Pattern)

**Solution Logic:** 
We implement a `StreamHub`. The RTMP Ingest connection is a **Publisher**. It pushes parsed tags to a "Topic". Consumers subscribe to the topic via `asyncio.Queue` (with size limits to drop frames for slow consumers). This decouples ingestion from processing.

# Q&A: Architecture Exploration Notes

**Q1:** If you scale ingest horizontally, what shared state in your current Python server would prevent you from just running multiple instances behind a load balancer?  
**Your answer:** Use RTMP as the ingest protocol (via something like nginx-rtmp) and avoid re-implementing RTMP parsing in Python.  
**Takeaway:** Let a battle-tested RTMP front handle protocol details; keep your service focused on stream metadata, backpressure, and fanout.

**Q2:** How would you represent transcoding as a “job” so workers can crash or restart without losing the stream? Should Redis be soft cache or hard source of truth?  
**Your answer:** Consider Redis, but you are unsure how critical that state should be.  
**Takeaway:** For live-only streams, Redis can usually act as soft coordination: if it restarts, you rebuild state when new RTMP pushes arrive. For anything billable or persistent, you need durable storage instead.

**Q3:** If 1000 viewers fetch segments directly from one box, what saturates first: CPU, disk, or NIC?  
**Your answer:** Probably the NIC.  
**Takeaway:** Roughly, `viewers * bitrate_per_viewer <= NIC_capacity`. With 1000 viewers at 4 Mbps, you need ~4 Gbps; a single 1 Gbps NIC will saturate, so you need either multiple edge nodes or a CDN in front of origin.

Next: [Prompt](../prompt.md)
