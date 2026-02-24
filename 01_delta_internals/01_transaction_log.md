
---

# 📘 WEEK 1 – DAY 1 - Delta Transaction Log

---

## 1️⃣ What Is the Delta Transaction Log?

Every Delta table has a directory:

```
_table_path/
    |_ parquet files
    |_ _delta_log/
```

The `_delta_log` directory is the **heart of Delta Lake**.

It contains the complete history of all changes made to the table.

Delta does NOT rely on modifying Parquet files directly.
It relies on this log to build consistent snapshots.

---

## 2️⃣ What Is Inside `_delta_log`?

Inside `_delta_log` you will find:

```
00000000000000000000.json
00000000000000000001.json
00000000000000000002.json
...
00000000000000000010.checkpoint.parquet
```

Each JSON file represents **one committed transaction**.

Version numbers increase sequentially.

If the table is at version 5, it means:

There are 6 commit files (0 → 5).

---

## 3️⃣ What Is a Commit File?

Each JSON file contains a list of **actions**.

Actions include:

* `add`
* `remove`
* `metaData`
* `protocol`
* `commitInfo`

Delta does not update files in place.
Instead, it records file-level changes as actions.

---

## 4️⃣ The `add` Action

When data is written, Delta creates new Parquet files.

Then it records an `add` action in the log.

An `add` action contains:

* path to the file
* file size
* modification time
* statistics (min/max values)
* partition values (if partitioned)

Example (simplified):

```json
{
  "add": {
    "path": "part-0000.parquet",
    "size": 12345,
    "partitionValues": {},
    "stats": {
      "minValues": {"id": 1},
      "maxValues": {"id": 100}
    }
  }
}
```

This tells Delta:

> “This file is now part of the table snapshot.”

---

## 5️⃣ The `remove` Action

When rows are deleted or updated, Delta does NOT modify files.

Instead:

* It writes new files
* It records `remove` actions for old files

Example:

```json
{
  "remove": {
    "path": "part-0000.parquet",
    "deletionTimestamp": 123456789
  }
}
```

This tells Delta:

> “This file should no longer be part of the active snapshot.”

The file still exists physically on storage.

It is only logically removed.

---

## 6️⃣ Copy-On-Write Behavior

This is critical.

When you run:

```sql
DELETE FROM table WHERE id = 1
```

Delta:

1. Reads the Parquet files that contain matching rows
2. Creates new Parquet files without the deleted rows
3. Writes `add` actions for new files
4. Writes `remove` actions for old files

It does NOT:

* Edit Parquet files
* Delete rows in place

This is called **copy-on-write**.

---

## 7️⃣ How Delta Builds a Snapshot

To read the current version:

Delta:

1. Starts at version 0
2. Applies all actions sequentially
3. Builds a list of active files:

   * add actions
   * minus remove actions

The result is a consistent snapshot.

This guarantees ACID properties.

---

## 8️⃣ What Guarantees ACID?

ACID is guaranteed by:

* The transaction log
* Atomic commits
* Snapshot isolation

A commit is atomic because:

* It writes a new version file
* That file represents a single transaction

If commit file exists → transaction succeeded
If it doesn’t → transaction never happened

No partial writes.

---

## 9️⃣ What Are Checkpoints?

After many JSON commits, Delta creates a checkpoint file:

```
00000000000000000010.checkpoint.parquet
```

Checkpoint contains:

* Consolidated state of the table at that version

Instead of replaying 1000 JSON files,
Delta reads the checkpoint + remaining JSON files.

This improves performance.

Checkpoint does NOT change data.
It improves log replay efficiency.

---

## 🔟 Why Time Travel Works

Time travel works because:

Old files are not immediately deleted.

If you query:

```sql
SELECT * FROM table VERSION AS OF 3
```

Delta:

* Reconstructs snapshot at version 3
* Uses files that were active at that version

Time travel only breaks if:

* VACUUM deletes old files physically

---

### What Happens If `_delta_log` Is Deleted?

If `_delta_log` is removed:

* Parquet files still exist
* But Delta metadata is gone
* ACID guarantees are gone
* Table is corrupted

The transaction log is mandatory.

---

### Summary — Professional-Level Understanding

Delta does NOT manage rows.
It manages files.

All operations:

* INSERT
* DELETE
* UPDATE
* MERGE

Are implemented as:

→ New file writes
→ add/remove actions in transaction log

Physical deletion only happens with:

```sql
VACUUM
```

---

# 🎯 Now Your Turn

Answer these:

1️⃣ When you delete 1 row from a Delta table, what physically happens?
Explain step-by-step.

2️⃣ Why can Delta guarantee atomic commits?

3️⃣ Why does time travel stop working after aggressive VACUUM?

Answer clearly.
We move to Day 2 only when your explanation is precise.

This is the foundation of everything else. 🚀
