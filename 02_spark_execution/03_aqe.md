
---

## 📘 WEEK 2 — DAY 3 — AQE (Adaptive Query Execution) & Join Strategy Selection

* Why did Spark switch join type?
* Why did performance improve without code change?
* Why is broadcast happening even though plan shows sort-merge?
* Why did shuffle partitions change at runtime?

All of that = AQE.

---

## 1️⃣ What Is AQE?

AQE = Adaptive Query Execution.

It allows Spark to modify the physical plan at runtime based on actual data statistics.

Important:

Logical plan does NOT change.
Physical plan can change during execution.

---

## 2️⃣ Why AQE Exists

Before AQE:

Spark had to decide join strategy before execution.

It estimated table sizes.

Estimates could be wrong.

With AQE:

Spark waits until shuffle stage finishes,
then uses real statistics to optimize further stages.

---

## 3️⃣ What AQE Can Do

AQE can:

1️⃣ Convert SortMergeJoin → BroadcastHashJoin
2️⃣ Coalesce shuffle partitions
3️⃣ Handle skewed joins

These are the 3 major AQE optimizations.

Professional exam expects you to know these precisely.

---

## 4️⃣ Join Strategy Selection

Spark join strategies:

* BroadcastHashJoin
* SortMergeJoin
* ShuffleHashJoin (less common)
* NestedLoopJoin (rare, expensive)

General rules:

Small table → Broadcast
Large tables → SortMerge
Skewed data → AQE may split partitions

---

## 5️⃣ Broadcast Join

Broadcast join:

* Small table sent to all executors
* No shuffle on large table
* Very fast for small dimension tables

Controlled by:

spark.sql.autoBroadcastJoinThreshold

Default ≈ 10MB (varies by version)

If table size < threshold → eligible for broadcast.

---

## 6️⃣ AQE Converting Join

Scenario:

Spark initially plans SortMergeJoin.

During execution:

It detects one side is small enough.

AQE converts it to BroadcastHashJoin.

Performance improves without code change.

Professional nuance:

explain() before execution may show SortMergeJoin.
Final executed plan may differ.

---

## 7️⃣ Coalescing Shuffle Partitions

If after shuffle:

Partitions are too small,

AQE can merge them to reduce overhead.

This reduces:

* Task scheduling cost
* Small task overhead

---

## 8️⃣ Skew Join Handling

If AQE detects skewed partition:

It can split heavy partition into smaller pieces.

This reduces long-running task bottlenecks.

Very important for skew-heavy joins.

---

## 9️⃣ What AQE Cannot Do

AQE does NOT:

* Change logical plan
* Change schema
* Scale cluster
* Remove partition columns
* Rewrite Delta files

It only optimizes physical execution.

Exam trap.

---

## 🔟 Professional Exam Traps

* Broadcast can happen even if original plan showed SortMergeJoin.
* Increasing shuffle partitions may be unnecessary if AQE coalesces.
* AQE does not fix bad partitioning strategy.
* AQE works at runtime after shuffle stage.

---

## 🧠 Deep Understanding Check

Answer clearly:

1. At what stage does AQE modify the plan?
2. Why can explain() show different join type than actual execution?
3. Why does broadcast join avoid shuffle on large table?
4. How does AQE handle skewed partitions?
5. Why does AQE not fix poor schema or partition design?