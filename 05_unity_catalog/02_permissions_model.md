
---

# 📅 Day 2 — Permission Model & Privilege Logic

This is heavily tested.

---

## 1️⃣ Privilege Hierarchy

To query table, user must have:

* USE CATALOG
* USE SCHEMA
* SELECT on table

Missing any → access denied.

This is very commonly tested.

---

## 2️⃣ Ownership Model

Each object has:

* Exactly one owner
* Owner can grant privileges
* Owner can transfer ownership

Ownership ≠ full metastore admin.

---

## 3️⃣ Common Privileges

Catalog-level:

* USE CATALOG
* CREATE SCHEMA

Schema-level:

* USE SCHEMA
* CREATE TABLE

Table-level:

* SELECT
* MODIFY
* DELETE
* OWN

Professional nuance:
Privileges do not automatically imply others across levels.

---

## 4️⃣ Inheritance Rules

Privileges granted at catalog level apply to objects inside.

But:

Table-level privileges still required for data access.

Exam scenario:
User has catalog access but cannot query table.

---

## 5️⃣ Managed vs External Permission Differences

Managed table:
UC controls data location automatically.

External table:
User must have:

* Access to external location
* Permission to use storage credential

Governance stricter for external tables.

---

