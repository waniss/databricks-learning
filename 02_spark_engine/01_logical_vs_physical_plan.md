
---

# 📄 01_logical_vs_physical_plan.md

# Logical Plan vs Physical Plan in Spark

---

## 1️⃣ What Is the Logical Plan?

The logical plan represents:

> What computation should be performed.

It describes:

* Projections
* Filters
* Joins
* Aggregations
* Expressions

It is independent of execution strategy.

Example:

```sql
SELECT customer_id, SUM(amount)
FROM transactions
GROUP BY customer_id
```

Logical intent:

* Scan table
* Group by customer_id
* Aggregate SUM

No execution detail yet.

---

## 2️⃣ What Is the Physical Plan?

The physical plan defines:

> How the computation will be executed.

It includes:

* Join strategy (BroadcastHashJoin, SortMergeJoin)
* Number of shuffle stages
* Exchange operators
* File scan details
* Partitioning behavior

Physical plan directly impacts:

* Performance
* Shuffle cost
* Memory pressure
* DBU consumption

---

## 3️⃣ Catalyst Optimizer Role

Spark uses Catalyst:

1️⃣ Builds logical plan
2️⃣ Applies rule-based optimization
3️⃣ Applies cost-based optimization
4️⃣ Generates physical plan

Catalyst decides:

* Join strategy
* Predicate pushdown
* Column pruning
* Reordering

Understanding this is critical for performance debugging.

---

## 4️⃣ Why Logical ≠ Physical Matters

Two queries with identical logical plans
may produce different physical plans.

Example:

Small dimension table:

→ BroadcastHashJoin

Large tables:

→ SortMergeJoin

Physical plan changes cost drastically.

Professional exam often hides this nuance.

---

## 5️⃣ Performance Impact

Physical plan determines:

* Shuffle volume
* Memory requirements
* Spill risk
* Network transfer
* CPU usage

Changing join strategy
can reduce cost dramatically.

---

## 6️⃣ Failure Modes

### Scenario A — Job Stuck at 99%

Likely skew in physical plan.

Logical plan fine.
Physical plan problematic.

---

### Scenario B — Unexpected Shuffle Explosion

Join strategy wrong.
Broadcast not used.

---

## 🔟 Professional Exam Traps

* Logical plan does not determine performance.
* Join strategy is physical-level decision.
* Broadcast avoids shuffle on large side.
* Physical plan inspection required for debugging.
* Same SQL can produce different execution strategies.

---

## 🧠 Deep Understanding Check

1. Why is physical plan more important than logical plan for cost?
2. How does Spark decide join strategy?
3. Why can two identical queries have different performance?
4. Why is shuffle expensive?
5. Why must you inspect physical plan in Spark UI?
