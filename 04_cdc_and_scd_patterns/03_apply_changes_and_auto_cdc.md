
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
