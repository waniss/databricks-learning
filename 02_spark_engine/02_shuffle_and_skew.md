
---

# 📄 02_shuffle_and_skew.md

# Shuffle Mechanics & Data Skew in Spark

---

## 1️⃣ What Is a Shuffle?

A shuffle occurs when Spark must:

> Redistribute data across partitions based on a key.

Common operations that trigger shuffle:

* `GROUP BY`
* `JOIN`
* `DISTINCT`
* `ORDER BY`
* `REPARTITION`

Shuffle =

* Data written to disk
* Transferred across network
* Read back into new partitions

It is one of the most expensive Spark operations.

---

## 2️⃣ Why Shuffle Is Expensive

Shuffle introduces:

* Disk I/O
* Network transfer
* Serialization/deserialization
* Barrier synchronization between stages

Cost impact:

* Increased runtime
* Increased memory pressure
* Possible spill to disk
* Increased DBU consumption

Shuffle is compute + network + disk combined.

---

## 3️⃣ Shuffle Stages

A shuffle creates:

Stage 1:

* Map tasks write shuffle files.

Stage 2:

* Reduce tasks read shuffle files.

Between them:

* Exchange operator.

Shuffle creates execution boundary.

---

## 4️⃣ What Is Data Skew?

Data skew occurs when:

> One partition receives disproportionately more data than others.

Example:

If 50% of rows share same key.

Then:

One reducer handles massive data.
Other reducers finish quickly.

Result:

Stage appears stuck at 99%.

---

## 5️⃣ Symptoms of Skew

* One task runs much longer
* High spill in one executor
* Uneven task duration
* Stage stuck at 99%

Spark UI reveals skew clearly.

---

## 6️⃣ Why Scaling Does Not Fix Skew

Adding more workers:

* Increases total partitions
* But skewed key still concentrated in one partition

Skew is distribution problem,
not cluster size problem.

Architectural fix required.

---

## 7️⃣ Skew Mitigation Techniques

1️⃣ Salting keys
2️⃣ Broadcast small side
3️⃣ AQE skew handling
4️⃣ Better partitioning
5️⃣ Data modeling improvements

Salting example:

Add random suffix to heavy key
to distribute load.

---

## 8️⃣ Cross-Domain Interaction

Shuffle & skew interact with:

* Join strategy
* Partitioning design
* AQE
* Memory instance type
* Cluster sizing
* Cost engineering

Most performance problems involve shuffle.

---

## 🔟 Professional Exam Traps

* Shuffle is network + disk heavy.
* Broadcast avoids shuffle on large side.
* Skew cannot be fixed by autoscaling alone.
* Partitioning affects shuffle.
* ZORDER does NOT reduce shuffle.
* None of Delta layout features fix shuffle directly.

---

## 🧠 Deep Understanding Check

1. Why is shuffle expensive?
2. Why does skew cause 99% stuck stage?
3. Why does increasing cluster size not fix skew?
4. How does broadcast reduce shuffle?
5. Why does partitioning not eliminate shuffle for joins?

---
