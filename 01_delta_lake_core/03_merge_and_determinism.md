# MERGE & Determinism

## 1️⃣ MERGE Mechanics

MERGE = Join + Rewrite + Commit.

---

## 2️⃣ Determinism Rule

Duplicate source rows matching same target → MERGE fails.
Always deduplicate source.

---

## 🔟 Exam Traps

- Clause order matters.
- MERGE rewrites files.
