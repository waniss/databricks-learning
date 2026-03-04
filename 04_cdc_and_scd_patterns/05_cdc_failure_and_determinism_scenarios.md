
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

