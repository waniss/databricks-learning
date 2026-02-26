
---

# 📘 WEEK 2 — DAY 2 — Shuffle Mechanics & Data Skew

---

## 1️⃣ What Is Shuffle?

Shuffle happens when data must move across partitions.

If an operation requires regrouping data by key → shuffle occurs.

Common shuffle triggers:

* groupBy
* join
* distinct
* orderBy
* repartition

No shuffle:

* filter
* select
* withColumn (unless causing aggregation)

---

## 2️⃣ What Physically Happens During Shuffle

When shuffle is triggered:

1. Each executor writes intermediate data to disk.
2. Data is partitioned by hash of shuffle key.
3. Data is transferred over network.
4. Target executors read their partition of data.
5. Next stage continues.

Shuffle involves:

* Disk I/O
* Network I/O
* Serialization
* Memory pressure

It is expensive.

---

## 3️⃣ Why Shuffle Is Expensive

Shuffle costs:

* CPU (serialization/deserialization)
* Network transfer
* Disk spill
* Task scheduling overhead

Large shuffle → slow job.

Professional insight:

Performance tuning often = reduce shuffle volume.

---

## 4️⃣ What Is Data Skew?

Data skew happens when:

One key has disproportionately more data than others.

Example:

Customer 123 has 40% of rows.

During shuffle:

* One partition gets huge amount of data.
* Other partitions finish quickly.
* One executor becomes bottleneck.

This causes:

* Job stuck at 99%
* Long single-task stage
* Underutilized cluster

---

## 5️⃣ Skew Example

Imagine 10 partitions.

Key distribution:

Partition 1 → 40% data
Other 9 partitions → 6–7% each

One executor works much longer.

That’s skew.

---

## 6️⃣ How To Detect Skew

Look at:

* Spark UI → stage details
* One task much longer than others
* One partition huge size

In explain plan:

You won’t see skew directly,
but physical plan will show shuffle.

---

## 7️⃣ How To Mitigate Skew

Professional-level strategies:

1️⃣ Salting
Add random key to distribute heavy key.

2️⃣ Broadcast smaller table
Avoid shuffle on large side.

3️⃣ Enable AQE skew join handling
Spark can split skewed partitions.

4️⃣ Repartition carefully

---

## 8️⃣ Salting Example

Instead of:

join on id

Use:

join on (id, random_bucket)

This spreads heavy key across partitions.

But requires adjustments on both sides.

---

## 9️⃣ Shuffle Partition Configuration

Parameter:

spark.sql.shuffle.partitions

Default: 200

Increasing partitions:

* More parallelism
* But more overhead

Too few partitions:

* Large tasks
* Memory pressure

Too many:

* Scheduling overhead

Professional exam loves this nuance.

---

## 🔟 Professional Exam Traps

* Increasing shuffle partitions does NOT always speed up job.
* Broadcast join avoids shuffle of large table.
* Data skew causes one slow task.
* Repartition always triggers shuffle.
* Coalesce reduces partitions without shuffle.

---

## 🧠 Deep Understanding Check

Answer clearly:

1. Why is shuffle expensive?
2. Why does data skew cause job imbalance?
3. How does salting reduce skew?
4. Why does repartition always cause shuffle?
5. Why does coalesce not cause shuffle (in most cases)?