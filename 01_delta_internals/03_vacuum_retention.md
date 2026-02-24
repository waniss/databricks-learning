
---

# 📘 WEEK 1 — DAY 3 — VACUUM

---

You already know:

* Delta uses copy-on-write
* Old files remain after UPDATE/DELETE
* Transaction log tracks add/remove

Now we answer:

> When are old files actually deleted?
> What can break if we delete too aggressively?

---

# 1️⃣ What Is VACUUM?

VACUUM is the operation that physically deletes files that are no longer referenced by the Delta table.

Important:

DELETE does not remove files physically.
UPDATE does not remove files physically.
MERGE does not remove files physically.

VACUUM is the only operation that removes obsolete files from storage.

---

## 2️⃣ Why Old Files Exist

After an UPDATE:

* Old file is marked as removed in `_delta_log`
* New file is added
* Old file still physically exists

Why?

Because Delta supports:

* Time travel
* Snapshot isolation
* Concurrent readers

Old snapshots may still require old files.

---

## 3️⃣ What VACUUM Does

When you run:

VACUUM table_name

Delta:

1. Identifies files no longer referenced in the latest snapshot
2. Checks retention period
3. Deletes files older than retention threshold

By default:

Retention = 7 days (168 hours)

Delta refuses to vacuum files newer than 7 days unless safety check is disabled.

---

## 4️⃣ Why 7 Days?

Because:

* Long-running jobs
* Streaming queries
* Time travel queries
* Concurrent reads

May still depend on older versions.

Deleting too early can break:

* Time travel
* Streaming checkpoints
* Running jobs

---

## 5️⃣ Dangerous Command

You can run:

VACUUM table_name RETAIN 0 HOURS

But by default, Delta prevents this.

You must disable safety:

spark.databricks.delta.retentionDurationCheck.enabled = false

This is extremely dangerous in production.



---

## 6️⃣ What Breaks If Retention Is Too Low?

If you vacuum aggressively:

1. Time travel may fail
2. Queries using VERSION AS OF may fail
3. Streaming jobs may fail
4. Concurrent jobs reading old snapshot may fail

Because required files are physically deleted.

---

## 7️⃣ Relationship With Time Travel

Time travel works because:

* Old files still exist
* Old versions still have valid file references

If VACUUM deletes those files:

Time travel to that version becomes impossible.

Delta cannot reconstruct snapshot without physical files.

---

## 8️⃣ How VACUUM Identifies Deletable Files

Delta:

* Looks at transaction log
* Determines which files are not part of current snapshot
* Checks file modification timestamp
* Applies retention threshold

Only files older than threshold AND not referenced are deleted.

---

## 9️⃣ Production Considerations

In production:

* Keep default 7-day retention unless you know what you're doing
* Ensure no long-running queries depend on old versions
* Be careful with streaming workloads
* Consider audit/compliance requirements

VACUUM is storage optimization — but must be used safely.

---

## 🔟 Professional Exam Traps

* DELETE does not free storage.
* UPDATE does not free storage.
* VACUUM is required for physical cleanup.
* RETAIN 0 HOURS can break time travel.
* Default retention is 7 days.
* Safety check prevents accidental data loss.

---

## 🧠 Deep Understanding Check

Answer clearly:

1. Why doesn’t DELETE reduce storage immediately?
2. Why does Delta block VACUUM with retention 0 by default?
3. What exactly breaks if required files are vacuumed?
4. Why is VACUUM dangerous in streaming pipelines?