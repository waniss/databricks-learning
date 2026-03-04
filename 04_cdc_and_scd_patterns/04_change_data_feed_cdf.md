
---

# 📄 04_change_data_feed_cdf.md

# Change Data Feed (CDF) — Row-Level Change Log

---

## 1️⃣ What Is CDF?

CDF is:

> A Delta feature that records row-level changes between table versions.

Unlike Time Travel (snapshot),
CDF shows:

* What changed.
* Not just final state.

---

## 2️⃣ Enable CDF

```sql
ALTER TABLE table_name
SET TBLPROPERTIES (delta.enableChangeDataFeed = true);
```

Not enabled by default.

---

## 3️⃣ Change Types

CDF adds _change_type column:

* insert
* delete
* update_preimage
* update_postimage

update_preimage:
Old version before update.

update_postimage:
New version after update.

Critical for SCD Type 2 logic.

---

## 4️⃣ Storage Mechanics

CDF writes additional files in:

```
_change_data/
```

Extra storage overhead.

Enable only when required.

---

## 5️⃣ Reading CDF

Batch:

```python
.option("readChangeData", "true")
.option("startingVersion", X)
```

Streaming:

Checkpoint handles version progression automatically.

---

## 6️⃣ VACUUM Interaction

VACUUM deletes CDF logs after retention.

If downstream job delayed beyond retention:

Job fails.

Default retention = 7 days.

CDF retention linked to Delta retention.

---

## 7️⃣ CDC Without CDF vs With CDF

Without CDF:

* Full table scan.
* Slower.
* Hard to detect deletes.

With CDF:

* Reads only changes.
* Efficient.
* Supports SCD Type 2 cleanly.

---

## 🔟 Professional Exam Traps

* CDF must be enabled explicitly.
* CDF is row-level change log.
* VACUUM deletes CDF after retention.
* CDF ≠ Time Travel.
* update_preimage required for SCD2.
* CDF increases storage footprint.

---

## 🧠 Deep Understanding Check

1. Why is CDF more efficient for CDC?
2. Why can VACUUM break CDF pipelines?
3. Why is update_preimage important?
4. Why is CDF not same as Time Travel?
5. When should CDF be enabled?


