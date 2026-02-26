
---

# 📘 WEEK 2 — DAY 1 — Logical Plan vs Physical Plan (Catalyst Deep Dive)

---

## 1️⃣ Spark Execution Pipeline

When you run:

```python
df.filter("country = 'FR'").groupBy("customer_id").count()
```

Spark does NOT execute top-to-bottom.

It goes through stages:

1. Parsed Logical Plan
2. Analyzed Logical Plan
3. Optimized Logical Plan
4. Physical Plan
5. Execution

Understanding this pipeline is critical.

---

## 2️⃣ Logical Plan

Logical plan describes:

* What operations should happen
* Not how they are executed

Example logical plan:

Filter → Aggregate → Project

It is abstract.

---

## 3️⃣ Optimized Logical Plan

Catalyst applies rules:

* Predicate pushdown
* Constant folding
* Column pruning
* Filter reordering

Example:

If you filter before groupBy,
Catalyst pushes filter down as early as possible.

This reduces data before shuffle.

---

## 4️⃣ Physical Plan

Physical plan decides:

* Join type (broadcast vs sort-merge)
* Shuffle stages
* Exchange operators
* File scan strategies

This is where cost-based decisions happen.

This is what determines performance.

---

## 5️⃣ How To Inspect It

Use:

```python
df.explain("extended")
```

This shows:

* Parsed Logical Plan
* Analyzed Logical Plan
* Optimized Logical Plan
* Physical Plan

Professional tip:

Always look for:

Exchange
BroadcastHashJoin
SortMergeJoin
HashAggregate

Exchange = shuffle.

---

## 6️⃣ Why This Matters For The Exam

Example question:

A query is slow due to shuffle.
What change improves performance?

If you understand physical plan, you look for:

* Unnecessary Exchange
* Large SortMergeJoin
* Missing broadcast
* Bad partitioning

Without plan understanding, you guess.

With plan understanding, you reason.

---

## 7️⃣ Example — Join Decision

Small table + large table.

Spark may choose:

SortMergeJoin (shuffle both sides)

But with broadcast threshold enabled:

BroadcastHashJoin (no shuffle of large side)

This is decided at physical plan stage.

AQE can change this at runtime.

---

## 8️⃣ Professional Exam Traps

* filter does NOT cause shuffle.
* groupBy causes shuffle.
* join usually causes shuffle.
* explain() does not execute query.
* Broadcast happens only if size threshold met.

---

## 🧠 Deep Understanding Check

Answer precisely:

1. What is the difference between logical plan and physical plan?
2. At which stage is join strategy chosen?
3. What does Exchange mean in physical plan?
4. Why does predicate pushdown improve performance?
