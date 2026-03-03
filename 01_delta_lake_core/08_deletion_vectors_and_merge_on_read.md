

# 📄 08_deletion_vectors_and_merge_on_read.md

# Deletion Vectors & Merge-on-Read Mechanics

---

## 1️⃣ The Legacy Model — Copy-on-Write (COW)

Historically, Delta Lake handled DELETE / UPDATE using:

> Copy-on-Write

If you delete 1 row from a 1GB file:

1️⃣ Read full 1GB file
2️⃣ Remove row in memory
3️⃣ Rewrite new ~999MB file
4️⃣ Mark old file as removed

Problem:

Tiny change → massive rewrite.

Heavy I/O.
High DBU cost.
Slow MERGE.

This is why large MERGE operations are expensive.

---

## 2️⃣ The Modern Model — Deletion Vectors (Merge-on-Read)

Deletion Vectors introduce:

> Merge-on-Read behavior

Instead of rewriting the entire Parquet file:

Delta writes a tiny sidecar file.

This file contains:

* A bitmap (bitmask)
* Marks specific row positions as deleted

The original Parquet file remains unchanged.

---

## 3️⃣ How Deletion Vectors Work

When a row is deleted:

1️⃣ Original file remains intact.
2️⃣ A small deletion vector file is created.
3️⃣ Vector indicates which row offsets are invalid.
4️⃣ Transaction log updated.

During read:

Spark:

* Reads Parquet file.
* Applies deletion vector.
* Skips deleted rows.

This shifts cost from write time to read time.

---

## 4️⃣ Why Deletion Vectors Matter

Benefits:

* Faster DELETE
* Faster UPDATE
* Faster MERGE
* Lower write I/O
* Reduced rewrite amplification

Small change no longer triggers large rewrite.

Massive performance improvement for update-heavy workloads.

---

## 5️⃣ Merge-on-Read vs Copy-on-Write

| Feature    | Copy-on-Write     | Deletion Vectors               |
| ---------- | ----------------- | ------------------------------ |
| Row Delete | Rewrite full file | Create small bitmap            |
| Write Cost | High              | Low                            |
| Read Cost  | Lower             | Slightly higher (apply vector) |
| MERGE      | Heavy rewrite     | More efficient                 |

Deletion Vectors optimize write-heavy pipelines.

---

## 6️⃣ Storage Mechanics

Deletion Vectors create:

* Additional small files
* Metadata overhead
* Sidecar vector storage

They are stored alongside table data.

Tradeoff:

Lower write cost
Slightly increased read complexity

---

## 7️⃣ Interaction with OPTIMIZE

When running:

```sql
OPTIMIZE table;
```

Delta may:

* Rewrite files
* Physically compact
* Remove deletion vectors
* Materialize clean files

OPTIMIZE can convert merge-on-read back into clean state.

Important nuance:

Deletion vectors do not eliminate need for OPTIMIZE.

---

## 8️⃣ Interaction with VACUUM

VACUUM:

* Deletes old files
* Also applies to deletion vector files

Retention still applies.

Deletion vectors do not bypass retention rules.

---

## 9️⃣ Performance Tradeoffs

Deletion Vectors are beneficial when:

* Many small deletes
* Frequent updates
* CDC-heavy workloads
* Streaming MERGE patterns

But:

If large portion of file deleted,
OPTIMIZE still recommended.

Otherwise:
Read performance may degrade.

---

## 🔟 Professional Exam Traps

* Deletion Vectors reduce write cost.
* They do not rewrite entire file.
* They shift cost to read.
* They do not eliminate OPTIMIZE.
* They are merge-on-read behavior.
* Copy-on-write rewrites files entirely.
* DV improves update-heavy workloads.
* DV does not change logical semantics.

---

## 🧠 Deep Understanding Check

1. Why was copy-on-write expensive for small deletes?
2. How do deletion vectors reduce write cost?
3. Why does merge-on-read shift cost to read time?
4. Why is OPTIMIZE still useful with DV?
5. Why are DV ideal for CDC-heavy workloads?

