

---

# 📄 04_uc_failure_and_permission_scenarios.md

# Unity Catalog Failure & Permission Diagnosis

---

## 1️⃣ User Sees Table but Cannot Query

Likely missing:

* SELECT privilege
* USE SCHEMA
* USE CATALOG

Hierarchy misconfiguration.

---

## 2️⃣ Cannot Create External Table

Likely missing:

* CREATE privilege
* Access to external location
* Storage credential permission

Governance failure.

---

## 3️⃣ User Can Create Table but Others Cannot Access

Likely:

* Missing grants
* Ownership not transferred
* Privileges not cascaded

Common governance mistake.

---

## 4️⃣ Cross-Workspace Access Fails

Possible causes:

* Workspace not attached to metastore
* Missing catalog access
* Metastore isolation issue

---

## 🔟 Professional Exam Traps

* Most UC issues are privilege hierarchy issues.
* External table creation requires storage credential.
* Workspace must be attached to metastore.
* Ownership determines grant capability.
* Missing USE SCHEMA blocks access.

---

## 🧠 Deep Understanding Check

1. Why can a user see table but not query it?
2. Why is external location required for external tables?
3. Why does ownership matter in troubleshooting?
4. Why does workspace attachment matter?
5. Why is privilege hierarchy often root cause?