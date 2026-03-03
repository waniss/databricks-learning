
---

# 📄 02_copy_on_write_and_file_lifecycle.md

# Copy-on-Write & File Lifecycle in Delta Lake

---

## 1️⃣ What Is Copy-on-Write?

Delta Lake does not update rows in place.

Instead, it uses:

> Copy-on-Write (COW)

When a row is modified:

The entire Parquet file containing that row is rewritten.

Delta is a file-level mutation system, not row-level.

---

## 2️⃣ Why Copy-on-Write Exists

Object storage does not support:

* In-place row mutation
* Partial file updates
* Block-level rewrite

Parquet files are immutable.

Therefore:

To modify a row:

* Read file
* Modify data in memory
* Write new file
* Mark old file as removed

Copy-on-Write guarantees:

* ACID safety
* Deterministic snapshots
* Isolation between readers and writers

---

## 3️⃣ What Physically Happens During UPDATE / DELETE

Example:

```sql
UPDATE table SET status = 'closed' WHERE id = 10;
```

Step-by-step:

1. Delta identifies files containing matching rows.
2. Reads those files.
3. Applies filter condition.
4. Creates new Parquet files with updated rows.
5. Writes JSON commit:

   * `remove` old files
   * `add` new files
6. Atomic commit finalizes version.

Old files remain in storage until VACUUM.

---

## 4️⃣ File Lifecycle in Delta

File states:

1️⃣ Created → active (`add`)
2️⃣ Rewritten → marked removed (`remove`)
3️⃣ Physically deleted → VACUUM

Important:

“remove” in log ≠ physical deletion.

Physical deletion only happens during VACUUM.

---

## 5️⃣ Performance Implications

Copy-on-Write means:

Updating 1 row in a 1GB file
→ rewrites 1GB file.

Performance impact depends on:

* File size
* Partitioning strategy
* Data clustering
* Update frequency

Heavy MERGE pipelines:
→ Large rewrite cost
→ Increased DBU consumption
→ Temporary storage growth

---

## 6️⃣ Storage Growth Dynamics

Frequent updates:

Old files accumulate.
Storage grows.

Without VACUUM:

Storage may double or triple over time.

Professional scenario:

Hourly MERGE for 3TB table
→ After weeks, storage ballooned.

Root cause:
Copy-on-Write accumulation.

---

## 7️⃣ Partitioning Interaction

If table partitioned by `event_date`:

Updating only one partition:
→ Only files in that partition rewritten.

If unpartitioned:
→ Potentially large rewrite scope.

Good partitioning reduces rewrite blast radius.

---

## 8️⃣ Interaction With ZORDER

ZORDER requires OPTIMIZE.

OPTIMIZE rewrites files.

Therefore:

ZORDER indirectly increases rewrite frequency.

Excessive clustering:
→ Rewrite cost increases.

---

## 9️⃣ Failure Modes

### Scenario A — Small File Explosion

Frequent updates + small files
→ Many rewrites
→ Scheduler overhead increases

---

### Scenario B — Merge Over Large Unpartitioned Table

High rewrite cost
Long runtime
High DBU usage

---

### Scenario C — Storage Cost Triples

Heavy MERGE
No VACUUM
Copy-on-Write accumulation

---

## 🔟 Cross-Domain Interaction

Copy-on-Write affects:

* MERGE cost
* OPTIMIZE cost
* ZORDER cost
* VACUUM necessity
* Streaming upsert patterns
* Cost engineering decisions

Understanding COW is essential for performance reasoning.

---

## 1️⃣1️⃣ Professional Exam Traps

* Delta never updates rows in place.
* One-row update rewrites entire file.
* remove action ≠ physical deletion.
* Storage growth after MERGE is expected.
* VACUUM required for cleanup.
* Partitioning reduces rewrite scope.

---

## 🧠 Deep Understanding Check

Answer precisely:

1. Why does updating one row rewrite entire file?
2. Why does storage increase after frequent MERGE?
3. How does partitioning reduce rewrite cost?
4. Why does VACUUM not affect active snapshot?
5. Why can heavy MERGE pipelines increase DBU cost?

---
