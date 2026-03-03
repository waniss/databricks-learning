Continuing Phase 2 — Spark Execution Engine.

---

# 📄 03_join_strategies_and_broadcast.md

# Join Strategies & Broadcast Mechanics in Spark

---

## 1️⃣ Why Join Strategy Matters

Joins are among the most expensive operations in Spark.

The chosen join strategy determines:

* Shuffle volume
* Memory usage
* Execution time
* Network cost
* DBU consumption

Same SQL join
→ completely different cost depending on strategy.

---

## 2️⃣ Main Join Strategies

Spark primarily uses:

1️⃣ Broadcast Hash Join
2️⃣ Sort Merge Join
3️⃣ Shuffle Hash Join (less common)

Each has different tradeoffs.

---

## 3️⃣ Broadcast Hash Join

Used when:

One table is small enough to fit in memory.

Spark:

1️⃣ Broadcasts small table to all executors.
2️⃣ Streams large table locally.
3️⃣ Performs local hash join.

Advantages:

* Avoids shuffle of large table.
* Very fast.
* Reduces network overhead.

Cost impact:

* Minimal shuffle.
* CPU + memory efficient.

---

## 4️⃣ Sort Merge Join

Used when:

Both tables are large.

Spark:

1️⃣ Shuffles both tables on join key.
2️⃣ Sorts partitions.
3️⃣ Merges sorted streams.

Cost:

* Full shuffle on both sides.
* Heavy network + disk.
* Sensitive to skew.

Sort Merge Join is stable but expensive.

---

## 5️⃣ Broadcast Threshold

Spark automatically broadcasts tables below threshold.

Config:

```python
spark.sql.autoBroadcastJoinThreshold
```

If small table exceeds threshold:
→ Broadcast not applied.

Professional trap:
Sometimes manually forcing broadcast improves performance.

---

## 6️⃣ When Broadcast Fails

Broadcast may not be used if:

* Statistics unavailable.
* Table slightly exceeds threshold.
* AQE disabled.
* Memory insufficient.

Manual hint possible:

```sql
SELECT /*+ BROADCAST(table) */ ...
```

---

## 7️⃣ Skew & Join Strategy

If skew exists:

* Broadcast join may still suffer on large side.
* Sort Merge Join may amplify skew.
* AQE may split skewed partitions.

Join strategy does not eliminate skew automatically.

---

## 8️⃣ Cross-Domain Interaction

Join strategy interacts with:

* Shuffle mechanics
* AQE
* Partitioning
* Cluster memory
* Cost engineering
* Streaming joins

Join decisions drive cost.

---

## 🔟 Professional Exam Traps

* Broadcast avoids shuffle on large side.
* Sort Merge requires shuffle on both sides.
* Increasing workers does not fix bad join strategy.
* ZORDER does NOT change join strategy.
* Broadcast threshold matters.
* Small dimension tables should be broadcast.

---

## 🧠 Deep Understanding Check

1. Why is Broadcast Hash Join cheaper?
2. Why is Sort Merge Join expensive?
3. When should you manually hint broadcast?
4. Why can join strategy change runtime drastically?
5. Why does partitioning not eliminate shuffle for joins?

---
