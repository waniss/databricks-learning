
---

# 📄 03_dbu_and_cost_tradeoffs.md

# DBU Economics & Cost Tradeoffs

---

## 1️⃣ What Is a DBU?

DBU = Databricks Unit.

Charged per node per hour.

Total cost =

Cluster size × DBU rate × runtime.

---

## 2️⃣ Bigger vs Smaller Cluster

Scenario:

Small cluster → 60 min
Large cluster → 20 min

If large cluster consumes 2× DBU/hour:

Large cluster may still be cheaper.

Cost depends on total runtime, not hourly rate alone.

---

## 3️⃣ Shuffle Cost Impact

Shuffle increases:

* Network usage
* Disk I/O
* Task time
* DBU consumption

Reducing shuffle often saves more than scaling cluster.

---

## 4️⃣ Streaming Cost Model

Streaming cluster runs continuously.

Cost drivers:

* Trigger interval
* Micro-batch size
* State store size
* Cluster size

Inefficient streaming = constant cost drain.

---

## 5️⃣ Rewrite Operations Cost

Expensive operations:

* MERGE
* OPTIMIZE
* ZORDER
* Large DELETE

These rewrite files.

Rewrite = compute + storage growth.

---

## 🔟 Professional Exam Traps

* Bigger cluster may be cheaper.
* Scaling does not fix skew.
* Rewrite-heavy workloads increase DBU.
* Streaming cost accumulates continuously.
* Shuffle drives cloud cost.

---

## 🧠 Deep Understanding Check

1. Why can larger cluster be cheaper?
2. Why does shuffle increase cost?
3. Why does streaming cost accumulate?
4. Why are rewrite operations expensive?
5. Why must architecture be fixed before scaling?

---