
---

#  WEEK 4 — STREAMING DEEP DIVE — Day 1 — How Structured Streaming REALLY Works

---

## 1️⃣ Micro-Batch Model (Core Architecture)

Structured Streaming (default mode) works as:

1. Read new data (based on offsets)
2. Create a micro-batch DataFrame
3. Execute full Spark job on that batch
4. Commit results + update checkpoint
5. Wait for next trigger

Important:

Each micro-batch is a full Spark job.

Not row-by-row processing.

---

## 2️⃣ Streaming = Incremental Batch

You can think of streaming as:

```
for each micro_batch:
    run batch job
```

This is why:

* Catalyst optimizer still applies
* AQE still applies
* Shuffle still happens
* Physical plan still matters

Streaming does NOT bypass Spark engine.

---

## 3️⃣ Where State Lives

For operations like:

* groupBy
* window
* join
* dropDuplicates

Spark must remember previous events.

This memory is called:

State Store

Stored in:

* Executor memory
* Backed by checkpoint storage

State grows over time unless cleaned.

---

## 4️⃣ What Happens Without Watermark

Example:

```python
df.groupBy("user_id").count()
```

In streaming:

Spark must remember ALL users forever.

State grows indefinitely.

Eventually:

* Memory pressure
* Disk spill
* Performance degradation
* Crash

Streaming without watermark on unbounded keys = dangerous.

---

## 5️⃣ Watermark — Deep Mechanics

Watermark tracks:

Maximum event_time seen so far.

Then:

Watermark time = max_event_time - delay_threshold

Example:

Max event_time seen = 10:30
Watermark delay = 10 minutes

Watermark = 10:20

Events older than 10:20 may be dropped from state.

Important:

Watermark moves forward only.

Never backward.

---

## 6️⃣ Watermark + Window Example

Example:

```python
df.withWatermark("event_time", "10 minutes") \
  .groupBy(window("event_time", "5 minutes")) \
  .count()
```

Spark:

* Keeps state for open windows
* Closes window once watermark passes window end
* Evicts old state

Without watermark:

Windows never close.

State never clears.

---

## 7️⃣ Exactly-Once — What It REALLY Means

Delta + Structured Streaming gives:

Exactly-once write semantics.

How?

Each micro-batch:

* Writes data
* Commits transaction
* Stores offset in checkpoint

If job crashes:

* It restarts
* Reads last committed offset
* Reprocesses safely

Duplicate writes avoided because:

Transaction log enforces atomicity.

Important nuance:

Exactly-once does NOT mean upstream events are unique.

It means sink writes are idempotent.

---

## 8️⃣ Checkpoint Anatomy

Checkpoint directory stores:

* offsets/
* commits/
* state/
* metadata

Without checkpoint:

Streaming job has no memory.

It restarts from beginning.

Checkpoint = streaming brain.

---

## 9️⃣ Common Streaming Failure Modes

1. State too large → OOM
2. No watermark → unbounded growth
3. VACUUM deleted needed files
4. Schema changed unexpectedly
5. Trigger interval too small → high overhead
6. Skew in streaming join

Professional exam often describes symptoms, not causes.

---

## 🔟 Performance Levers

You can tune:

* Trigger interval
* State TTL via watermark
* Shuffle partitions
* Broadcast joins
* File compaction

Streaming still obeys Spark performance rules.

---

## 🧠 Deep Check — No Guessing

Answer precisely:

1. Why does streaming without watermark cause memory growth?
2. Why does watermark depend on max event time?
3. What guarantees exactly-once semantics?
4. Why is checkpoint required for fault tolerance?
5. Why can a streaming join cause skew?