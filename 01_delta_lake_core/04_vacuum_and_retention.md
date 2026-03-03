
---

# 📄 04_vacuum_and_retention.md

# VACUUM & Retention in Delta Lake

---

## 1️⃣ What Is VACUUM?

VACUUM is the operation that:

> Physically deletes files that are no longer referenced by the transaction log.

Important distinction:

* `remove` in log = logical deletion
* `VACUUM` = physical deletion

Without VACUUM:
Storage grows over time.

---

## 2️⃣ Why Retention Exists

Delta default retention:

```sql
7 days
```

Why?

Because:

* Streaming jobs may reference older files
* Time travel depends on retained files
* Concurrent readers may still rely on older snapshots
* Accidental deletes need safety window

Retention is a safety mechanism.

---

## 3️⃣ What Physically Happens During VACUUM

Steps:

1️⃣ Delta scans transaction log.
2️⃣ Identifies files no longer referenced.
3️⃣ Applies retention threshold.
4️⃣ Deletes eligible files from storage.

VACUUM does NOT modify the log.

It only deletes files.

---

## 4️⃣ Aggressive VACUUM (Danger Zone)

Example:

```sql
VACUUM table RETAIN 0 HOURS;
```

If retention check disabled:

* Files immediately deleted.
* Time travel broken.
* Streaming jobs may fail.
* Snapshot reconstruction may fail.

Professional trap:
Aggressive VACUUM is rarely correct in production.

---

## 5️⃣ Time Travel & VACUUM

Time travel depends on:

* Old log entries
* Old Parquet files

If Parquet files deleted:
Time travel to that version fails.

Log still exists.
Data does not.

---

## 6️⃣ Streaming + VACUUM Interaction

Streaming reads Delta incrementally.

If streaming job:

* Has checkpoint referencing older version
* And VACUUM deletes those files

Next micro-batch may fail with:

“File not found”

Default 7-day retention protects streaming.

---

## 7️⃣ Cost Considerations

Without VACUUM:

* Storage cost increases
* Old files accumulate
* Cloud storage requests increase

With aggressive VACUUM:

* Risk of breaking jobs
* Loss of time travel
* Operational instability

Architectural balance required.

---

## 8️⃣ Failure Modes

### Scenario A — Streaming Fails After Maintenance

Admin runs aggressive VACUUM.
Streaming job fails next day.

Root cause:
Files deleted while still needed.

---

### Scenario B — Storage Tripled

Heavy MERGE.
No VACUUM scheduled.

Root cause:
Copy-on-Write accumulation.

---

### Scenario C — Time Travel Fails

VACUUM deleted required files.

---

## 🔟 Professional Exam Traps

* VACUUM deletes files, not log entries.
* Default retention = 7 days.
* VACUUM can break streaming.
* remove action ≠ deletion.
* Aggressive VACUUM is dangerous.
* Time travel depends on retained files.

---

## 🧠 Deep Understanding Check

1. Why does VACUUM not affect active snapshot?
2. Why is default retention 7 days?
3. How can VACUUM break streaming?
4. Why can storage grow without VACUUM?
5. Why is RETAIN 0 HOURS rarely safe?

---