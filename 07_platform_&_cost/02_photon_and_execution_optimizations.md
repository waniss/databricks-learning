---

# 📄 02_photon_and_execution_optimizations.md

# Photon & Execution Optimization

---

## 1️⃣ What Is Photon?

Photon is:

> A native vectorized execution engine for Databricks.

Optimized for:

* CPU-bound workloads
* SQL-heavy analytics
* Aggregations
* Joins

---

## 2️⃣ What Photon Improves

Photon improves:

* Expression evaluation
* Aggregation speed
* Join processing
* Scan efficiency

Reduces runtime.

Shorter runtime → lower total DBU cost (often).

---

## 3️⃣ What Photon Does NOT Fix

Photon does not:

* Fix skew
* Reduce shuffle volume
* Eliminate small file problems
* Prevent copy-on-write rewrites

It optimizes execution,
not architecture.

---

## 4️⃣ Cost Implications

Photon may:

* Use slightly higher DBU rate
* But reduce total runtime

Net effect often cheaper overall.

Exam frequently tests this reasoning.

---

## 🔟 Professional Exam Traps

* Photon improves CPU-bound workloads.
* Photon does not reduce shuffle.
* Photon does not fix skew.
* Faster execution can reduce total cost.
* Photon operates at execution engine level.

---

## 🧠 Deep Understanding Check

1. Why can Photon reduce total cost?
2. Why does Photon not fix skew?
3. When is Photon most beneficial?
4. Why does Photon not eliminate shuffle?
5. Why is Photon not a substitute for good layout?

---