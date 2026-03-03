
---

# 📄 02_checkpointing_and_exactly_once.md

# Checkpointing & Exactly-Once Semantics

---

## 1️⃣ What Is a Checkpoint?

Checkpoint is:

> The persistent storage of streaming progress and state.

Stored in a directory (e.g., `/checkpoint`).

Contains:

* Offsets
* Commit logs
* State store data
* Query metadata

Checkpoint is the “memory” of the streaming job.

---

## 2️⃣ Why Checkpointing Exists

Without checkpoint:

* Job restarts from beginning.
* State lost.
* Duplicate writes possible.

Checkpoint enables:

* Fault tolerance
* Progress tracking
* Incremental processing

---

## 3️⃣ What Exactly-Once Means

Exactly-once means:

> Each record is written once to the sink — even in case of failure.

Important nuance:

Exactly-once applies to supported sinks like Delta.

It does NOT guarantee:

* No duplicate events upstream
* No duplicate source data

It guarantees sink-level idempotency.

---

## 4️⃣ How Exactly-Once Works with Delta

For each micro-batch:

1️⃣ Process data.
2️⃣ Write to Delta.
3️⃣ Commit Delta transaction.
4️⃣ Record offset in checkpoint.

If crash occurs:

* Before commit → batch reprocessed.
* After commit → offset prevents duplication.

Atomic commit + checkpoint coordination ensures correctness.

---

## 5️⃣ When Exactly-Once Breaks

If:

* Custom sink without transactional guarantee.
* Checkpoint directory deleted.
* VACUUM removed required files.

Then:

Duplicate writes or failure may occur.

---

## 6️⃣ Checkpoint Deletion Scenario

If checkpoint lost:

Streaming restarts from earliest available offset.

Consequences:

* Full replay
* Possible duplicate writes (if sink not idempotent)
* Increased cost

Checkpoint must be protected.

---

## 🔟 Professional Exam Traps

* Exactly-once applies to supported sinks only.
* Checkpoint stores state and offsets.
* Deleting checkpoint causes replay.
* Exactly-once does not prevent upstream duplicates.
* Checkpoint is required for fault tolerance.

---

## 🧠 Deep Understanding Check

1. Why is checkpoint required for streaming fault tolerance?
2. Why does exactly-once depend on the sink?
3. What happens if checkpoint is deleted?
4. Why is offset commit coordination important?
5. Why is Delta well-suited for streaming sinks?

---

# 📄 03_watermarks_and_late_data.md

# Watermarks & Late Data Handling

---

## 1️⃣ What Is a Watermark?

Watermark defines:

> How late data is allowed to arrive before being dropped.

Example:

```python
.withWatermark("event_time", "10 minutes")
```

Watermark time = max_event_time_seen − delay.

---

## 2️⃣ Why Watermarks Exist

Without watermark:

State grows indefinitely.

Watermarks allow:

* State eviction
* Window closure
* Memory control

Watermarks prevent unbounded state.

---

## 3️⃣ How Watermark Works

For each micro-batch:

1️⃣ Track max event_time seen.
2️⃣ Compute watermark threshold.
3️⃣ Evict state older than watermark.

Watermark moves forward only.

Never backward.

---

## 4️⃣ Late Data Behavior

If data arrives later than watermark threshold:

It may be dropped.

This is correctness vs memory tradeoff.

Aggressive watermark:

* Lower memory
* Higher risk of data loss

Lenient watermark:

* Higher memory
* Better correctness

Architectural balance required.

---

## 5️⃣ Watermarks & Windows

Windows close only when watermark passes window end.

Without watermark:
Windows never close.
State persists forever.

---

## 🔟 Professional Exam Traps

* Watermark does not reorder data.
* Watermark is eviction mechanism.
* Late data beyond threshold may be dropped.
* Watermark affects memory footprint.
* No watermark → state growth.

---

## 🧠 Deep Understanding Check

1. Why does watermark depend on max event time?
2. Why is watermark a tradeoff?
3. Why does state grow without watermark?
4. Why can aggressive watermark cause data loss?
5. Why must windows rely on watermark?