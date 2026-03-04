
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
