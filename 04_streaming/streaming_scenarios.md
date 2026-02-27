
---

# WEEK 4 — DAY 3 — Streaming Failure Diagnosis Drill

---

# 🧩 Scenario 1 — Streaming Memory Explosion

Symptoms:

* Streaming job running 3 weeks
* Memory usage keeps increasing
* Executors start spilling
* Eventually OOM
* Query uses groupBy on user_id
* No watermark defined

### Questions

1. Root cause?
2. Why doesn’t scaling cluster fix it?
3. Correct fix?

---

# 🧩 Scenario 2 — Streaming Job Fails After Table Maintenance

Symptoms:

* Admin runs aggressive VACUUM (0 hours)
* Streaming job fails next morning
* Error: “File not found” referencing old Delta version

### Questions

1. What happened?
2. Why did default retention exist?
3. Safe maintenance strategy?

---

# 🧩 Scenario 3 — Duplicate Rows in Silver Table

Pipeline:

* Streaming Bronze → MERGE into Silver
* Occasionally duplicates appear
* No errors thrown

### Questions

1. Why duplicates possible?
2. What is missing?
3. Correct design?

---

# 🧩 Scenario 4 — Late Data Disappears

Streaming query:

```python
.withWatermark("event_time", "5 minutes")
```

Business reports:

Events delayed 10 minutes are missing.

### Questions

1. Why missing?
2. Why increasing cluster size won’t help?
3. Correct architectural decision?

---

# 🧩 Scenario 5 — Streaming Restart Reprocesses Old Data

Symptoms:

* Checkpoint accidentally deleted
* Streaming restarted
* Bronze data duplicated in Silver

### Questions

1. Why did this happen?
2. Why exactly-once did not protect?
3. Correct operational practice?

---

# 🧩 Scenario 6 — Streaming Join Stuck at 99%

Symptoms:

* Stream joined with static 2TB table
* One task runs much longer
* AQE enabled
* Large skew in join key

### Questions

1. Root cause?
2. Why AQE may not fully fix?
3. Correct fix?

---

# 🧠 Instructions

Reply like:

Scenario 1:
1.
2.
3.
