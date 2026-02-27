Excellent 💪
Now we enter the **exam-heavy trap zone**.

---

## WEEK 4 — DAY 2 — Streaming + Delta + VACUUM + Schema Evolution (Edge Cases)

This is where many Professional candidates lose points.

Questions won’t ask:

“What is a watermark?”

They’ll ask:

> A streaming job suddenly fails after maintenance. Why?

You must connect Delta internals + streaming mechanics.

---

## 1️⃣ Streaming Reading from Delta — What Actually Happens

When you do:

```python
spark.readStream.format("delta").load("/path")
```

Spark:

1. Reads transaction log
2. Tracks new commits
3. Processes new files incrementally
4. Stores offsets in checkpoint

Important:

Streaming reads Delta log versions, not raw files.

---

## 2️⃣ VACUUM + Streaming — Dangerous Combination

Default VACUUM retention = 7 days.

Why?

Because streaming may still reference old files.

If someone runs:

```sql
VACUUM table RETAIN 0 HOURS;
```

And disables retention check:

Old files are physically deleted.

If streaming job needs those files (for state or replay):

→ Job fails.

---

#### What Actually Breaks?

Streaming checkpoint stores:

* Offsets
* Version information

If referenced files no longer exist:

Delta cannot reconstruct snapshot.

Streaming throws file-not-found or version errors.

---

## 3️⃣ Schema Evolution in Streaming

Case 1 — Add column

Safe if:

* mergeSchema enabled
* Sink supports evolution
* Downstream logic handles NULLs

Case 2 — Change column type

Not safe.

Streaming job may fail because:

* Schema mismatch
* Incompatible type change
* State store schema mismatch

Streaming schema changes are stricter than batch.

---

## 4️⃣ Streaming + MERGE (Upserts)

Common pattern:

Bronze → Silver streaming with MERGE.

Example:

For each micro-batch:

```python
merge into silver_table
```

Important nuance:

MERGE:

* Rewrites files
* Heavy operation
* Must deduplicate source

If duplicates exist in micro-batch:

MERGE fails.

Streaming + MERGE amplifies copy-on-write cost.

---

## 5️⃣ State Store & Schema Change

If you:

* Add aggregation column
* Change key structure
* Modify grouping logic

Checkpoint state becomes incompatible.

Result:

Streaming job may fail or require checkpoint reset.

Checkpoint reset = data replay risk.

Professional exam may describe:

“Streaming restarted from scratch unexpectedly.”

Often due to checkpoint deletion or schema change.

---

## 6️⃣ Idempotency in Streaming

Streaming jobs must be idempotent.

Why?

Because if micro-batch reprocesses:

You don’t want duplicates.

Delta sink ensures transactional atomicity.

But custom sinks may not.

Professional nuance:

Exactly-once applies to supported sinks like Delta.

---

## 7️⃣ Late Data + Watermark Trap

If watermark = 10 minutes

And data arrives 20 minutes late

That data is dropped.

If business requires correctness:

Watermark too aggressive.

Architect must balance:

Memory vs correctness.

---

## 8️⃣ Common Streaming Failure Scenarios

###### Scenario A

Streaming fails after VACUUM.

Root cause:
Retention too low.

---

###### Scenario B

Streaming memory keeps growing.

Root cause:
No watermark.

---

###### Scenario C

Streaming fails after schema change.

Root cause:
State store incompatibility.

---

###### Scenario D

Streaming MERGE fails intermittently.

Root cause:
Duplicate source rows.

---

###### Scenario E

Streaming slow and expensive.

Root cause:
Tiny trigger interval + large cluster.

---

## 9️⃣ Professional Exam Traps

* VACUUM can break streaming.
* Watermark does not guarantee event order.
* mergeSchema does not fix type conflicts.
* MERGE rewrites files even in streaming.
* Checkpoint deletion causes full replay.
* Exactly-once is sink-level guarantee.

---

## 🔟 Advanced Insight — Why 7 Days Default Exists

Default retention protects:

* Long-running streaming jobs
* Replay scenarios
* Time travel
* Concurrent reads

Aggressive VACUUM breaks guarantees.

---

## 🧠 Deep Understanding Check

Answer sharply:

1. Why can VACUUM break streaming jobs?
2. Why are streaming schema changes risky?
3. Why is MERGE expensive in streaming?
4. Why must streaming sinks be idempotent?
5. Why is watermark a tradeoff decision?
