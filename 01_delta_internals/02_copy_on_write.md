



---
# 📘 WEEK 1 — DAY 2 — COPY-ON-WRITE
---

## 1. Core Principle

Delta Lake operates at the file level, not at the row level.

Parquet files are immutable.
They cannot be modified in place.

Therefore, when a row is updated or deleted, Delta must rewrite entire files that contain affected rows.

This mechanism is called **copy-on-write**.

---

## 2. Why Copy-On-Write Is Necessary

Object storage systems (S3, ADLS, GCS):

* Do not support row-level mutation
* Do not support in-place updates
* Do not provide ACID guarantees

Parquet format:

* Is immutable
* Is optimized for reads, not updates

To guarantee ACID behavior, Delta:

* Writes new files
* Marks old files as removed
* Records everything in the transaction log

---

## 3. What Physically Happens During UPDATE

Suppose you execute:

UPDATE table SET value = 'X' WHERE id = 1

Delta performs the following steps:

1. Locate Parquet files containing rows where id = 1.
2. Read those files.
3. Apply the update in memory.
4. Write new Parquet file(s) with modified rows.
5. Record in `_delta_log`:

   * remove action for old file(s)
   * add action for new file(s)

Old files remain physically in storage until VACUUM removes them.

No Parquet file is modified in place.

---

## 4. Concrete Example

File A (500MB):

Contains 1 million rows.
Row with id = 1 is inside this file.

You update only that row.

Delta will:

* Rewrite the full 500MB file
* Produce a new 500MB file
* Mark old file as removed
* Add new file as active

Even though only one row changed.

---

## 5. Storage Impact

After an UPDATE:

* Old file still exists
* New file exists
* Both consume storage

Until VACUUM runs, storage temporarily increases.

Heavy update workloads can significantly increase storage footprint.

---

## 6. Performance Impact

Copy-on-write increases:

* Write latency (full file rewrite)
* Storage cost (duplicate versions)
* Metadata size (more add/remove actions)
* Commit size in transaction log

Frequent MERGE operations amplify this effect.

---

## 7. Architectural Tradeoff

Large files:

* Better read performance
* Lower metadata overhead
* Expensive updates (large rewrites)

Small files:

* Cheaper rewrites
* Worse read performance
* Higher scheduling overhead

System design must balance read efficiency and update cost.

---

## 8. Relationship With Other Concepts

Copy-on-write directly connects to:

* Transaction log (add/remove actions)
* VACUUM (physical deletion)
* MERGE behavior
* Small file problem
* File size optimization strategy

---

## 9. Why This Guarantees ACID

Because files are immutable:

* Old data remains intact
* New data is written separately
* Commit is atomic via log file
* Snapshot isolation is preserved

No partial updates can occur.

---

## 10. Professional Exam Insights

* Delta never updates Parquet files in place.
* Updating 1 row can rewrite a very large file.
* MERGE is implemented using copy-on-write.
* Storage growth after UPDATE is normal.
* VACUUM is required for physical cleanup.

---

Now I want you to answer clearly:

1. Why does updating 1 row rewrite the entire file?
2. Why does storage increase after UPDATE?
3. Why can frequent MERGE operations become expensive?

Answer in 5–6 precise sentences total.

If your explanation is sharp, tomorrow we move to:

Day 3 — VACUUM, Retention Strategy & Time Travel Safety (very exam-critical).

We are building depth now, not surface knowledge. 🚀
