
---

# WEEK 4 — UNITY CATALOG TRACK

---

# 📅 Day 1 — Architecture & Storage Model

## 1️⃣ Why Unity Catalog Exists

Problems before UC:

* Hive metastore per workspace
* No centralized governance
* No consistent permissions across workspaces
* Weak lineage & audit

Unity Catalog provides:

* Account-level metastore
* Cross-workspace governance
* Fine-grained access control
* Centralized storage management
* Data lineage & auditability

Professional takeaway:

UC is governance-first architecture.

---

## 2️⃣ Object Hierarchy (Must Be Perfect)

Hierarchy:

Metastore
→ Catalog
→ Schema
→ Table / View / Function

Example:

prod_metastore
→ analytics
→ finance
→ transactions

Permissions inherit downward.

---

## 3️⃣ Managed vs External Tables (Deep Understanding)

### Managed Table

* Storage controlled by UC
* Stored in managed storage location
* DROP TABLE deletes data
* Recommended for fully governed systems

### External Table

* Data stored in external cloud path
* UC manages metadata only
* DROP TABLE does NOT delete data
* Requires external location

Exam trap:

Dropping external table does NOT delete files.

---

## 4️⃣ Storage Credentials & External Locations

Storage Credential:

* Represents cloud IAM role or service principal.

External Location:

* Secured mapping to cloud path.
* Requires permission to use.

To create external table:
You need access to external location.

Common exam failure:
User has CREATE TABLE but not access to external location.

---

## 5️⃣ Workspace Attachment Model

Metastore is account-level.

Multiple workspaces can attach to same metastore.

This allows:

* Shared governance
* Central security
* Environment isolation

Important distinction:
UC is not workspace-scoped.