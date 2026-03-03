
---

# 📄 01_uc_architecture_and_object_model.md

# Unity Catalog Architecture & Object Model

---

## 1️⃣ What Is Unity Catalog?

Unity Catalog (UC) is:

> The centralized governance layer for data & AI assets in Databricks.

It provides:

* Fine-grained access control
* Cross-workspace governance
* Centralized metadata
* Auditability

UC operates at **account level**, not workspace level.

---

## 2️⃣ Why Unity Catalog Exists

Before UC:

* Each workspace had its own metastore.
* Governance fragmented.
* No centralized control.
* Hard to audit access.

UC solves:

* Centralized governance
* Shared catalog across workspaces
* Uniform access control

---

## 3️⃣ Object Hierarchy

Unity Catalog hierarchy:

```text
Metastore
  └── Catalog
        └── Schema
              └── Table / View / Function
```

Hierarchy matters for permission inheritance.

You cannot access a table without:

* USE CATALOG
* USE SCHEMA
* Object-level privilege

Hierarchy logic is heavily tested.

---

## 4️⃣ Metastore Scope

Metastore:

* Attached at account level
* Can serve multiple workspaces
* Controls catalogs globally

Workspace attaches to one metastore.

Professional nuance:
Multiple metastores possible per account (advanced setups).

---

## 5️⃣ Governance Benefits

UC enables:

* Column-level security
* Row-level filters
* Central auditing
* External location control
* Cross-team isolation

Governance is centralized, not workspace-local.

---

## 🔟 Professional Exam Traps

* UC is account-level, not workspace-level.
* Access requires catalog + schema + object privileges.
* Workspace attachment required to access UC.
* Governance is centralized.
* Metastore controls catalogs globally.

---

## 🧠 Deep Understanding Check

1. Why is UC account-level governance?
2. Why can’t you access table without USE SCHEMA?
3. Why is centralized metastore important?
4. How does UC improve auditing?
5. Why does hierarchy matter in privilege evaluation?

---