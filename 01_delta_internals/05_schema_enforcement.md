
---

# 📘 WEEK 1 — DAY 5 — Schema Enforcement


---

## 1️⃣ Schema Enforcement — What It Is

Schema enforcement means:

Delta validates incoming data against the existing table schema.

If the incoming schema does not match:

→ The write fails.

Example:

Table schema:

```
id INT
value STRING
```

Incoming DataFrame:

```
id INT
value STRING
new_column STRING
```

Write without schema evolution:

→ Write fails.

Delta protects you from accidental schema corruption.

---

## 2️⃣ Schema Evolution — What It Does

Schema evolution allows Delta to automatically add new columns.

Enabled with:

```sql
.option("mergeSchema", "true")
```

or

```sql
ALTER TABLE ADD COLUMN
```

With mergeSchema enabled:

New columns are appended to the table schema.

Existing files are not rewritten.

Old rows will have NULL for the new column.

---

## 3️⃣ Enforcement vs Evolution — Key Difference

Schema Enforcement:

* Rejects schema mismatch.
* Protects table consistency.

Schema Evolution:

* Accepts new columns.
* Extends schema safely.

Important:

Evolution does not modify old files.
It only updates metadata.

---

## 4️⃣ What Happens Physically During Evolution

If you add a new column:

Delta:

1. Updates metadata in `_delta_log`.
2. Does NOT rewrite existing Parquet files.
3. Writes new files with new schema.

Old files remain as-is.
Query engine handles missing columns as NULL.

This is metadata-driven schema handling.

---

## 5️⃣ Dangerous Scenario

If you accidentally write wrong data type:

Example:

Table column:

```
id INT
```

Incoming:

```
id STRING
```

Delta rejects write.

This is schema enforcement preventing corruption.

Professional exam trap:

Spark (without Delta) might allow unsafe behavior.
Delta blocks it.

---

## 6️⃣ Column Mapping (Advanced Concept)

Delta supports column mapping modes:

* name-based mapping (default modern behavior)
* position-based mapping (older behavior)

Why this matters:

If schema changes column order,
name-based mapping prevents data corruption.

Professional nuance:

Column mapping ensures logical column identity remains stable even if schema evolves.

---

## 7️⃣ Schema Overwrite

Using:

```sql
.mode("overwrite")
.option("overwriteSchema", "true")
```

This replaces entire schema.

Dangerous in production.

Can break downstream systems.

Exam may test difference between:

* mergeSchema
* overwriteSchema

| Aspect             | mergeSchema          | overwriteSchema |
| ------------------ | -------------------- | --------------- |
| Adds columns       | Yes                  | Yes             |
| Removes columns    | No                   | Yes             |
| Changes types      | No                   | Yes             |
| Rewrites old files | No                   | Often yes       |
| Risk level         | Medium               | High            |
| Typical use        | Controlled evolution | Table redesign  |


---

## 8️⃣ Production Considerations

In production:

* Enable mergeSchema carefully.
* Avoid silent schema drift.
* Use explicit ALTER TABLE when possible.
* Monitor schema changes.
* Avoid accidental type widening.

Schema governance is critical.

---

## 9️⃣ Relationship With Other Topics

Schema handling interacts with:

* MERGE (source schema mismatch)
* Streaming (schema changes break queries)
* Unity Catalog (governance)
* Column-level security

---

## 🔟 Professional Exam Traps

* Schema enforcement blocks incompatible writes.
* mergeSchema adds columns but does not rewrite old data.
* overwriteSchema replaces entire schema.
* Evolution does not update historical data.
* Column mapping prevents positional corruption.

---

## 🧠 Deep Understanding Check

Answer precisely:

1. Why does Delta block writes with incompatible schema by default?
2. What happens physically when mergeSchema is enabled?
3. Why is overwriteSchema dangerous?
4. Why does schema evolution not rewrite old files?

