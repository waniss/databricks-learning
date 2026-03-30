
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
