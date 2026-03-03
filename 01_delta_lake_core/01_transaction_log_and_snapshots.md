
---

# 📄 07_partitioning_vs_zorder_vs_liquid.md

# Partitioning vs ZORDER vs Liquid Clustering

---

## 1️⃣ Partitioning

Directory-based data organization.

Example:

```text
/table/date=2024-01-01/
```

Pros:

* Partition pruning
* Efficient for low-cardinality columns

Cons:

* High cardinality → millions of folders
* Metadata explosion
* Small file risk

---

## 2️⃣ ZORDER

File-level clustering.

Improves:

* Data skipping
* File pruning

Not incremental.

Requires OPTIMIZE.

Rewrites files fully.

---

## 3️⃣ Liquid Clustering

Modern clustering approach.

Advantages:

* Flexible clustering
* No rigid directory structure
* Adaptive clustering

Reduces partitioning rigidity.

---

## 4️⃣ What These Do NOT Do

They do NOT:

* Reduce shuffle
* Change join strategy
* Modify execution plan

They affect file layout, not compute logic.

---

## 5️⃣ Cost & Rewrite Impact

Partitioning:

* Influences rewrite scope

ZORDER:

* Heavy rewrite
* Not incremental

Liquid:

* More flexible
* Still rewrite-based

---

## 6️⃣ Failure Modes

High cardinality partitioning → millions of folders.

ZORDER overuse → rewrite explosion.

Wrong clustering column → no performance gain.

---

## 🔟 Professional Exam Traps

* Partitioning is not for high-cardinality keys.
* ZORDER is not incremental.
* None of these fix skew.
* ZORDER requires OPTIMIZE.
* Liquid clustering reduces rigid partitions.

---

## 🧠 Deep Understanding Check

1. Why is high-cardinality partitioning dangerous?
2. Why is ZORDER not incremental?
3. Why does partitioning not reduce shuffle?
4. When is Liquid clustering preferred?
5. Why can excessive ZORDER increase cost?
