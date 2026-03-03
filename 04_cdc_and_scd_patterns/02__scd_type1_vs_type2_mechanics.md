Perfect.
I will now generate **all remaining files for the new CDC domain**, using maximum relevant content from your document  and integrating it cleanly into our structured format.

We complete:

```
06_cdc_and_scd_patterns/
  02_scd_type1_vs_type2_mechanics.md
  03_apply_changes_and_auto_cdc.md
  04_change_data_feed_cdf.md
  05_cdc_failure_and_determinism_scenarios.md
```

---

# 📄 02_scd_type1_vs_type2_mechanics.md

# SCD Type 1 vs Type 2 — Design & Storage Mechanics

---

## 1️⃣ What Is SCD?

SCD (Slowly Changing Dimension) is:

> A data modeling pattern that defines how changes to dimension records are stored over time.

Important:

SCD is the **design pattern**.
CDC is the **change capture strategy**.

SCD defines how the target table should look.

---

## 2️⃣ SCD Type 1 (Overwrite)

Type 1:

* Overwrites existing row.
* No history retained.
* Only latest value stored.

Example:

User changes address.

Old row overwritten.

Target table always reflects current state.

---

### When to Use Type 1

* You only care about latest state.
* No historical analytics required.
* Storage optimization prioritized.

---

### Storage Behavior

Type 1 uses:

* UPDATE logic
* No version columns
* No effective date range

Simplest implementation.

---

## 3️⃣ SCD Type 2 (History Tracking)

Type 2:

* Preserves full history.
* Adds new row per change.
* Maintains validity window.

Columns typically include:

* __start_at
* __end_at
* __is_current

From your document :

Lakeflow manages these automatically when stored_as_scd_type = 2.

---

## 4️⃣ How Type 2 Works Mechanically

When UPDATE arrives:

1️⃣ Close old row (set __end_at).
2️⃣ Mark old row __is_current = false.
3️⃣ Insert new row with new values.
4️⃣ Set __start_at.
5️⃣ __is_current = true.

DELETE:

May close record instead of physically deleting.

---

## 5️⃣ Why Type 2 Is Complex

Requires:

* Deterministic sequence logic
* Correct ordering
* Handling late-arriving data
* Proper merge semantics
* Update_preimage logic (if using CDF)

Without sequence_by:
Type 2 easily corrupts history.

---

## 6️⃣ Performance Impact

Type 2:

* Inserts more rows.
* Increases table size.
* Increases join complexity.
* More rewrite operations.

Type 2 more expensive than Type 1.

---

## 🔟 Professional Exam Traps

* Type 1 overwrites.
* Type 2 preserves history.
* Type 2 requires deterministic ordering.
* Type 2 requires validity window columns.
* Late-arriving updates complicate Type 2.
* APPLY CHANGES automates Type 2 management.

---

## 🧠 Deep Understanding Check

1. Why is Type 2 more complex than Type 1?
2. Why is sequence ordering critical for Type 2?
3. Why does Type 2 increase storage?
4. Why is __is_current useful?
5. Why can late updates corrupt Type 2?

---

# 📄 03_apply_changes_and_auto_cdc.md

# APPLY CHANGES INTO & Auto CDC Flow

---

## 1️⃣ What Is APPLY CHANGES?

APPLY CHANGES INTO is:

> A declarative CDC API provided in Lakeflow Pipelines.

It automates:

* MERGE logic
* Sequence resolution
* Delete handling
* SCD Type 1 & 2 behavior

Removes need for manual MERGE complexity.

---

## 2️⃣ Core Parameters

From your document :

```python
create_auto_cdc_flow(
  target="target_table",
  source="cdc_source",
  keys=["key_field"],
  sequence_by=col("operation_date"),
  apply_as_deletes=expr("operation_type = 'DELETE'"),
  except_column_list=["operation_type","operation_date"],
  stored_as_scd_type=1
)
```

---

## 3️⃣ keys

Defines:

Primary key of record.

Used to match incoming change with existing row.

Without correct key:
Upsert logic fails.

---

## 4️⃣ sequence_by (Critical)

Determines ordering.

Highest value wins.

Prevents:

Older update overwriting newer state.

Mandatory for correctness.

---

## 5️⃣ apply_as_deletes

Defines delete condition.

If not defined:
Deletes ignored.

Important nuance:
Deletes must be explicitly modeled.

---

## 6️⃣ except_column_list

Used to:

Exclude metadata columns from target.

Keeps Silver/Gold clean.

Without this:
Operational columns pollute business table.

---

## 7️⃣ stored_as_scd_type

Options:

* 1 → Overwrite
* 2 → Full history

Lakeflow automatically manages Type 2 fields.

Professional nuance:
Target must support update semantics.

---

## 🔟 Professional Exam Traps

* sequence_by mandatory.
* Target must be Streaming Table.
* Deletes must be defined.
* APPLY CHANGES safer than manual MERGE.
* SCD Type 2 auto-manages validity windows.
* Metadata columns should be excluded.

---

## 🧠 Deep Understanding Check

1. Why is sequence_by required?
2. Why must delete logic be explicit?
3. Why exclude operational columns?
4. Why is APPLY CHANGES safer?
5. Why must target support updates?

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

---

# 📄 05_cdc_failure_and_determinism_scenarios.md

# CDC Failure Modes & Determinism Pitfalls

---

## 1️⃣ Out-of-Order Events

If:

UPDATE arrives before INSERT.

Without sequence_by:
Data corrupted.

Fix:
Always define deterministic ordering.

---

## 2️⃣ Duplicate Keys in Batch

Multiple changes for same key in same micro-batch.

If not deduplicated:
MERGE fails.

APPLY CHANGES handles ordering better.

---

## 3️⃣ Late-Arriving Changes

Late change may:

* Reopen closed SCD2 row incorrectly.
* Override correct record.

Requires correct sequence logic.

---

## 4️⃣ Delete Handling Omitted

If delete logic missing:

Deleted records remain active.

Data integrity broken.

---

## 5️⃣ CDF Retention Expired

Downstream job starts late.

CDF data gone.

Pipeline fails.

---

## 🔟 Professional Exam Traps

* Determinism is central to CDC.
* sequence_by is mandatory.
* Deletes must be defined.
* SCD2 sensitive to ordering.
* CDF retention matters.
* Duplicate keys cause failures.

---

## 🧠 Deep Understanding Check

1. Why does out-of-order data corrupt CDC?
2. Why must duplicate keys be handled?
3. Why can late data break SCD2?
4. Why does CDF retention matter?
5. Why is determinism central to CDC?

