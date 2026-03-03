
---

# 📄 04_streaming_with_delta_and_merge.md

# Streaming with Delta & MERGE Patterns

---

## 1️⃣ Streaming to Delta

Typical pattern:

```python
df.writeStream.format("delta")
```

Streaming writes create new Delta versions incrementally.

Delta transaction log tracks each micro-batch.

---

## 2️⃣ Streaming Upserts (MERGE Pattern)

Common CDC pattern:

```python
foreachBatch(lambda df, batch_id: ...)
```

Each batch executes MERGE.

Implications:

* Join cost per batch.
* File rewrite per batch.
* Accumulated copy-on-write overhead.

Streaming MERGE amplifies rewrite cost.

---

## 3️⃣ Performance Risks

Frequent micro-batches + MERGE:

* Many small rewrites
* Small file explosion
* Increased storage
* Increased DBU cost

Requires:

* Deduplication
* Auto Optimize
* Periodic OPTIMIZE

---

## 4️⃣ Determinism in Streaming MERGE

Duplicate rows in micro-batch:

→ MERGE fails.

Deduplication required before MERGE.

Streaming pipelines must enforce idempotency.

---

## 5️⃣ VACUUM Interaction

If streaming references older versions:

Aggressive VACUUM may delete required files.

Streaming fails.

Retention default protects this.

---

## 🔟 Professional Exam Traps

* Streaming MERGE is rewrite-heavy.
* Deduplication required before MERGE.
* VACUUM can break streaming.
* Auto Optimize helps streaming ingestion.
* Streaming cost accumulates over time.

---

## 🧠 Deep Understanding Check

1. Why is streaming MERGE expensive?
2. Why must micro-batches be deduplicated?
3. Why can aggressive VACUUM break streaming?
4. Why is Auto Optimize useful in streaming?
5. Why does streaming cost accumulate steadily?
