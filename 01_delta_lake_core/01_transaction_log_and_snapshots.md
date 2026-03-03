# Delta Transaction Log & Snapshot Isolation

## 1️⃣ What Is the Delta Transaction Log?

Delta Lake is a log-structured table format layered on immutable Parquet files.
The `_delta_log` directory defines table state.
Without `_delta_log`, Parquet files are just files.

---

## 2️⃣ Why It Exists

Object storage lacks:
- ACID guarantees
- Atomic multi-file commits
- Row-level mutation

Delta solves this using immutable files + transaction log + optimistic concurrency control.

---

## 3️⃣ Snapshot Construction

Snapshot = all add actions − all remove actions at version N.
Readers always see consistent snapshot.

---

## 4️⃣ Write Mechanics

INSERT → add new files.
UPDATE/MERGE → rewrite files + remove old + add new.
Parquet files are never mutated.

---

## 🔟 Professional Exam Traps

- Delta manages files, not rows.
- Snapshot built from log, not directory listing.
- VACUUM deletes physical files only.
