Excellent.
Now we simulate real Professional-level reasoning.

No theory.

Only architecture decisions.

---

#  WEEK 3 — DAY 5 — End-to-End Production Architecture Scenario Drill

You are the architect.

For each scenario:

* Identify root problem
* Identify wrong instinct solution
* Give correct architectural solution
* Explain why

Keep answers short and sharp.

---

## 🧩 Scenario 1 — Job Stuck at 99%

A daily batch job:

* Runs on 16-worker cluster
* Processes 2TB table
* Stuck at 99% for 40 minutes
* Spark UI shows one task much longer than others

### Question

1. What is the root cause?
2. Why won’t increasing cluster size fix it?
3. What is the correct fix?

---

## 🧩 Scenario 2 — Storage Cost Tripled

Delta table:

* 3TB originally
* Heavy MERGE every 2 hours
* After 3 weeks → 9TB storage
* VACUUM not scheduled

### Question

1. Why did storage increase?
2. What operation is missing?
3. What is the safe strategy?

---

## 🧩 Scenario 3 — Query Slow After 6 Months

Fact table:

* Partitioned by event_date
* ZORDER(customer_id) run once 6 months ago
* Queries filtering on customer_id now slow

### Question

1. Why did performance degrade?
2. Why doesn’t partitioning help?
3. What should be done?

---

## 🧩 Scenario 4 — Streaming Job Fails After Maintenance

Streaming job reading Delta table.

Admin runs:

```sql
VACUUM table RETAIN 0 HOURS;
```

Streaming job fails next day.

### Question

1. Why did it fail?
2. What was deleted?
3. Why is 7-day default important?

---

## 🧩 Scenario 5 — Team Repartitioned 3TB Table

To improve query:

```sql
SELECT * FROM table WHERE customer_id = 123;
```

Team decides to repartition table by customer_id.

Result:

* Millions of folders
* Small file explosion
* Worse performance

### Question

1. Why was repartitioning wrong?
2. What was correct solution?
3. What modern alternative exists?

---

## 🧩 Scenario 6 — Cost Too High for Streaming

Streaming job:

* Trigger every 1 second
* Micro-batches tiny
* 8-node cluster running 24/7

Finance complains about cost.

### Question

1. Why is cost inefficient?
2. What tuning changes reduce cost?
3. Would scaling cluster fix cost?

---

## 🧩 Scenario 7 — Merge Failure in Production

Nightly MERGE fails with:

“Multiple source rows matched the same target row.”

### Question

1. Why does Delta fail here?
2. What is missing in pipeline?
3. How should source be prepared?

---

## 🧩 Scenario 8 — Slow Join Between 5MB & 2TB Table

Join:

Dimension table → 5MB
Fact table → 2TB

Spark chooses SortMergeJoin.

### Question

1. What join should be used?
2. Why might Spark not broadcast?
3. What config influences this?

---

## 🧠 Instructions

Reply with:

Scenario 1:
1.
2.
3.

Scenario 2:
1.
2.
3.
