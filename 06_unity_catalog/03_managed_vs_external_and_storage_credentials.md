

---

# 📄 03_managed_vs_external_and_storage_credentials.md

# Managed vs External Tables & Storage Credentials

---

## 1️⃣ Managed Tables

Managed tables:

* Data stored in UC-controlled storage location.
* DROP TABLE deletes both metadata and data.

Fully governed by UC.

---

## 2️⃣ External Tables

External tables:

* Data stored in external storage path.
* DROP TABLE removes metadata only.
* Data remains in storage.

Used for:

* Shared data
* Cross-system integration
* Controlled storage locations

---

## 3️⃣ Storage Credentials

Storage credentials define:

> How UC accesses cloud storage.

They represent:

* IAM role
* Service principal
* Access identity

Required for external locations.

---

## 4️⃣ External Locations

External location:

* Path in cloud storage
* Secured via storage credential
* Governed by UC

Without external location:
Cannot create external table.

---

## 5️⃣ Security Implications

Managed tables:

* Strong governance
* Data lifecycle tied to UC

External tables:

* Governance + external lifecycle
* More flexibility
* More complexity

Professional exam often tests:

DROP behavior differences.

---

## 🔟 Professional Exam Traps

* DROP managed table deletes data.
* DROP external table keeps data.
* External table requires external location.
* Storage credential required for access.
* Governance applies to metadata and access.

---

## 🧠 Deep Understanding Check

1. Why is managed table safer?
2. Why does DROP external table not delete data?
3. Why are storage credentials required?
4. Why use external tables?
5. Why is external location necessary?
