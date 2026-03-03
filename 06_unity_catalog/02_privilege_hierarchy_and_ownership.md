---

# 📄 02_privilege_hierarchy_and_ownership.md

# Privilege Model & Ownership Rules

---

## 1️⃣ Privilege Layers

To query a table, user must have:

1️⃣ USE CATALOG
2️⃣ USE SCHEMA
3️⃣ SELECT on table

Missing any → access denied.

Hierarchy is cumulative.

---

## 2️⃣ Ownership

Each object has:

* One owner

Owner can:

* Grant privileges
* Modify object
* Transfer ownership

Ownership is powerful.

---

## 3️⃣ Inheritance Model

Privileges do NOT automatically cascade downward.

Granting USE CATALOG does NOT grant SELECT on all tables.

Explicit grants required.

Professional trap:
Hierarchy ≠ automatic full inheritance.

---

## 4️⃣ Common Privileges

Common permissions:

* SELECT
* MODIFY
* CREATE
* USAGE (USE)
* EXECUTE (functions)

Granular privileges enable least-privilege model.

---

## 5️⃣ Row & Column Security

UC supports:

* Row-level filters
* Column masking

These enforce fine-grained access.

Exam may test scenario:

User sees table but certain rows masked.

---

## 🔟 Professional Exam Traps

* USE CATALOG alone is insufficient.
* Ownership is single-entity.
* Privileges are explicit.
* Row-level filters enforce dynamic restrictions.
* Granting CREATE SCHEMA does not grant SELECT on tables.

---

## 🧠 Deep Understanding Check

1. Why must USE SCHEMA be granted explicitly?
2. Why is ownership powerful?
3. Why is privilege inheritance limited?
4. Why are row filters useful?
5. Why does least-privilege matter?