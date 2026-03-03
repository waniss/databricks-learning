
---

# 📄 04_adaptive_query_execution.md

# Adaptive Query Execution (AQE)

---

## 1️⃣ What Is AQE?

Adaptive Query Execution allows Spark to:

> Modify the physical execution plan at runtime based on observed statistics.

Instead of committing to one static plan,
Spark can adapt after shuffle stages.

AQE works only at the physical plan level.

---

## 2️⃣ Why AQE Exists

At planning time, Spark estimates:

* Table sizes
* Partition distribution
* Cardinality

These estimates may be wrong.

AQE improves decisions using:

* Actual runtime statistics
* Real shuffle data sizes

---

## 3️⃣ What AQE Can Do

AQE can:

1️⃣ Switch join strategy (e.g., SortMerge → Broadcast)
2️⃣ Coalesce small shuffle partitions
3️⃣ Split skewed partitions
4️⃣ Optimize skew joins

These changes happen after shuffle stage completes.

---

## 4️⃣ Join Strategy Switching

Example:

Initial plan:
SortMergeJoin.

After first shuffle:
Spark detects small side is tiny.

AQE switches to:
BroadcastHashJoin.

This reduces second shuffle stage.

---

## 5️⃣ Skew Handling

If one partition is much larger:

AQE can:

* Split large partition
* Redistribute workload

This reduces 99% stuck stage issues.

Important:

AQE mitigates skew,
but does not eliminate root cause.

---

## 6️⃣ Partition Coalescing

If too many small partitions exist:

AQE merges them.

Benefits:

* Fewer tasks
* Lower scheduler overhead
* Improved resource usage

---

## 7️⃣ Performance Impact

AQE improves:

* Join performance
* Shuffle efficiency
* Resource utilization

However:

AQE does not fix:

* Bad partitioning
* Poor data modeling
* Excessive small files
* Copy-on-write cost

---

## 8️⃣ Failure Modes

### Scenario A — Broadcast Not Applied

AQE disabled.

Fix:
Enable AQE.

---

### Scenario B — Still Skew After AQE

Data skew too extreme.
Requires architectural fix.

---

## 🔟 Professional Exam Traps

* AQE operates at physical level only.
* AQE can change join strategy at runtime.
* AQE mitigates skew but does not fix bad data design.
* AQE does not reduce rewrite cost in Delta.
* AQE does not change logical plan.

---

## 🧠 Deep Understanding Check

1. Why is AQE superior to static planning?
2. When can AQE switch join type?
3. Why does AQE not fix small file problems?
4. How does AQE mitigate skew?
5. Why is AQE not a substitute for good architecture?

---