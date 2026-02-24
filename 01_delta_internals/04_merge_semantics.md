
---

# 🗓 WEEK 1 — DAY 4 — MERGE

---

## 1️⃣ What Is MERGE?

MERGE combines:

* UPDATE
* INSERT
* DELETE

In a single atomic transaction.

Typical pattern:

MERGE INTO target t
USING source s
ON t.id = s.id
WHEN MATCHED THEN UPDATE
WHEN NOT MATCHED THEN INSERT

MERGE is implemented using copy-on-write.

---

## 2️⃣ What Physically Happens During MERGE

Internally, Delta:

1. Identifies files in target containing rows that match join condition.
2. Performs join between source and target.
3. Applies MATCHED / NOT MATCHED logic.
4. Rewrites affected files.
5. Records add/remove actions in transaction log.

It is a file rewrite operation, not row mutation.

---

## 3️⃣ Critical Rule — Determinism

MERGE must be deterministic.

If multiple source rows match the same target row:

MERGE fails.

Why?

Because:

* Delta cannot deterministically decide which source row to apply.
* Applying both would produce ambiguous results.

This is one of the most common Professional exam traps.

---

## 4️⃣ Duplicate Match Example

Target:

id | value
1  | A

Source:

id | value
1  | X
1  | Y

Both source rows match id=1.

Result?

MERGE fails with error.

Delta requires source deduplication before MERGE.

---

## 5️⃣ Clause Ordering Matters

MERGE allows:

WHEN MATCHED AND condition THEN UPDATE
WHEN MATCHED AND condition THEN DELETE

Order matters.

First matching clause is applied.

Professional nuance:

Improper ordering can cause unexpected behavior.

---

## 6️⃣ NOT MATCHED BY SOURCE

Advanced clause:

WHEN NOT MATCHED BY SOURCE THEN DELETE

Used in:

* Full sync pipelines
* Slowly changing dimensions
* CDC full refresh

Important nuance:

This can rewrite many files and be very expensive.

---

## 7️⃣ Performance Impact of MERGE

MERGE can be expensive because:

* It performs join between source and target.
* It rewrites entire affected files.
* It may affect large partitions.

Performance depends on:

* Partitioning strategy
* File size
* Data skew
* Number of matching keys

MERGE-heavy pipelines must be optimized carefully.

---

## 8️⃣ Common Production Mistakes

* Not deduplicating source before MERGE.
* Merging into unpartitioned large table.
* Running MERGE on huge dataset without filtering.
* Ignoring small file explosion after frequent merges.

---

## 9️⃣ Relationship With Other Concepts

MERGE interacts with:

* Copy-on-write (file rewrite)
* Transaction log (add/remove)
* VACUUM (cleanup)
* Partition pruning
* ZORDER (data skipping)
* AQE (join optimization)

---

## 🔟 Professional Exam Traps

* MERGE fails if duplicate source rows match same target row.
* MERGE rewrites files even if only few rows change.
* Clause order matters.
* NOT MATCHED BY SOURCE can delete large amounts of data.
* MERGE is not magically efficient — it’s join + rewrite.

---

## 🧠 Deep Understanding Check

Answer clearly:

1. Why does MERGE fail when duplicate source rows match same target row?
2. Why can MERGE be very expensive on large tables?
3. How does partitioning affect MERGE performance?
4. Why must source data often be deduplicated before MERGE?
