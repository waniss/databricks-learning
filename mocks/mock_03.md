
---

# WEEK 4 — DAY 4  Mock Exam Simulation — Performance + Delta + Streaming Mixed

---

## 🔹 Q1 — Shuffle Optimization

A job joins:

* 5MB dimension table
* 1.5TB fact table

Spark uses SortMergeJoin.

What is the best optimization?

A) Increase shuffle partitions
B) Enable broadcast join
C) Repartition fact table
D) Increase cluster size

---

## 🔹 Q2 — Storage Growth

Delta table size doubled in 2 weeks.

Heavy MERGE every hour.
VACUUM never scheduled.

Most likely reason?

A) Data corruption
B) Copy-on-write accumulation
C) ZORDER overuse
D) Shuffle spill

---

## 🔹 Q3 — ZORDER Drift

ZORDER applied 4 months ago.
Table continuously appended.
Query performance degrading.

Best explanation?

A) AQE disabled
B) ZORDER not incremental
C) Partition pruning failed
D) Photon disabled

---

## 🔹 Q4 — Streaming Crash After Maintenance

Streaming reading Delta table.
Admin runs:

VACUUM RETAIN 0 HOURS.

Streaming fails next day.

Why?

A) Checkpoint deleted
B) State store corrupted
C) Required Delta files removed
D) Broadcast timeout

---

## 🔹 Q5 — Cluster Strategy

Batch job runs once per day.
Team uses always-on interactive cluster.

Best improvement?

A) Autoscaling
B) Spot instances
C) Switch to job cluster
D) Increase workers

---

## 🔹 Q6 — Partitioning Mistake

3TB table partitioned by customer_id.
Millions of directories.
Performance degraded.

Root problem?

A) AQE disabled
B) High cardinality partitioning
C) No ZORDER
D) No broadcast

---

## 🔹 Q7 — Streaming Memory Leak

Streaming groupBy without watermark.
Memory usage increasing.

Why?

A) Shuffle partitions too high
B) No state eviction
C) Too many executors
D) Wrong trigger interval

---

## 🔹 Q8 — MERGE Failure

MERGE fails with:

“Multiple source rows matched same target row.”

Correct fix?

A) Increase cluster size
B) Deduplicate source
C) Disable AQE
D) Repartition target

---

## 🔹 Q9 — Cost Optimization

Job CPU-bound and heavy aggregation.
What reduces cost most?

A) More workers
B) Memory-optimized instances
C) Enable Photon
D) Increase shuffle partitions

---

## 🔹 Q10 — Small File Explosion

Streaming writing every second.
Thousands of tiny files.

Best mitigation?

A) Increase cluster size
B) Repartition downstream
C) Enable Auto Optimize
D) ZORDER

---
