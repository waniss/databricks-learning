

# 📄 01_cdc_fundamentals.md

# Change Data Capture (CDC) — Strategy & Mechanics

---

## 1️⃣ What Is CDC?

CDC (Change Data Capture) is:

> The strategy of capturing INSERT, UPDATE, and DELETE changes from a source system and applying them to a target table.

Important distinction:

CDC is the **strategy** (the “what”).
MERGE or APPLY CHANGES is the **implementation** (the “how”).

CDC is not a Delta feature by itself.

It is a data engineering pattern.

---

## 2️⃣ Why CDC Exists

Without CDC:

* You must reload entire tables.
* Expensive full-table scans.
* High compute cost.
* Risk of overwriting correct data.
* No incremental efficiency.

CDC allows:

* Incremental updates
* Low-latency replication
* Efficient synchronization
* Correct historical modeling

CDC is fundamental for Bronze → Silver pipelines.

---

## 3️⃣ What a CDC Stream Looks Like

CDC systems typically provide:

| key | value | operation | timestamp |
| --- | ----- | --------- | --------- |
| 1   | A     | INSERT    | t1        |
| 1   | B     | UPDATE    | t2        |
| 1   | null  | DELETE    | t3        |

CDC includes:

* Primary key
* Operation type
* Sequence column (very important)

Without sequence ordering,
data corruption is possible.

---

## 4️⃣ The Critical Concept: sequence_by

This is mandatory: sequence_by ensures the “highest” value wins.

Why?

If:

UPDATE arrives before INSERT due to network lag,
and no sequence logic exists,
you corrupt the target.

sequence_by ensures:

Latest event wins deterministically.

This is one of the most important Professional exam nuances.

---

## 5️⃣ CDC Implementation Options

You can implement CDC using:

1️⃣ Manual MERGE
2️⃣ APPLY CHANGES INTO (Lakeflow)
3️⃣ create_auto_cdc_flow API

Manual MERGE:

* More control
* More complexity
* Risk of determinism errors

APPLY CHANGES:

* Declarative
* Safer
* Less error-prone
* Handles SCD automatically

Professional exam often favors APPLY CHANGES.

---

## 6️⃣ Deletes in CDC

Deletes must be handled explicitly.

From your document:

```python
apply_as_deletes = expr("operation_type = 'DELETE'")
```

If you do not define delete logic:

* Target table never removes deleted records.
* Data becomes inconsistent.

Delete handling is not automatic.

---

## 7️⃣ CDC Without CDF vs With CDF

Your document explains a critical distinction:

### Without CDF

* Spark scans full table.
* Slower as table grows.
* Hard to detect row-level deletes.
* Source must provide clean change stream.

### With CDF

* Spark reads row-level change log.
* Efficient.
* Handles update_preimage & update_postimage.
* Required for SCD Type 2 automation.

This is a Professional-level comparison.

---

## 8️⃣ Performance Implications

CDC performance depends on:

* Source volume
* Sequence ordering
* Deduplication
* Merge frequency
* File layout
* CDF usage

Without CDF:
Cost increases over time.

With CDF:
Reads only change log.
Stable performance.

---

## 🔟 Professional Exam Traps

* CDC is strategy, not feature.
* sequence_by is mandatory.
* Delete logic must be explicit.
* CDC without CDF scales poorly.
* APPLY CHANGES simplifies complex MERGE.
* Incorrect ordering corrupts data.
* CDC requires deterministic logic.

---

## 🧠 Deep Understanding Check

1. Why is sequence_by mandatory in CDC?
2. Why can network lag corrupt CDC pipelines?
3. Why is CDC without CDF less efficient?
4. Why must deletes be handled explicitly?
5. Why is APPLY CHANGES safer than manual MERGE?

---

