
# 📄 05_schema_enforcement_and_evolution.md

# Schema Enforcement & Evolution in Delta

---

## 1️⃣ Schema Enforcement

Delta enforces schema by default.

If you attempt:

```sql
INSERT INTO table VALUES (...)
```

With incompatible schema:
Operation fails.

This protects:

* Data quality
* Downstream pipelines
* Analytics correctness

---

## 2️⃣ Why Enforcement Exists

Without enforcement:

* Silent schema drift
* Column misalignment
* Corrupt analytical logic

Delta ensures structural integrity.

---

## 3️⃣ Schema Evolution

Enable evolution:

```python
.option("mergeSchema", "true")
```

Allows:

* Adding new columns
* Schema extension

It does NOT allow:

* Incompatible type change
* Dropping columns automatically

---

## 4️⃣ overwriteSchema

Used with:

```python
.mode("overwrite").option("overwriteSchema", "true")
```

Replaces entire schema.

Dangerous in production.

Can break downstream consumers.

---

## 5️⃣ Streaming Schema Changes

Streaming is stricter:

* State store depends on schema
* Schema change may invalidate checkpoint
* May require checkpoint reset

Resetting checkpoint:
→ Full replay risk.

---

## 6️⃣ Performance & Cost Impact

Frequent schema evolution:

* Forces metadata updates
* May require file rewrite
* Can impact query planning

---

## 7️⃣ Failure Modes

### Scenario A — Streaming Fails After Column Type Change

State store incompatible.

---

### Scenario B — Silent Nulls After Column Added

Downstream logic not updated.

---

## 🔟 Professional Exam Traps

* mergeSchema allows additive change only.
* overwriteSchema replaces schema entirely.
* Streaming schema changes are risky.
* Schema enforcement protects from drift.
* Evolution does not fix type conflicts.

---

## 🧠 Deep Understanding Check

1. Why is schema enforcement critical in analytics?
2. Why is overwriteSchema dangerous?
3. Why can streaming break after schema change?
4. What type of change does mergeSchema allow?
5. Why is silent schema drift dangerous?
