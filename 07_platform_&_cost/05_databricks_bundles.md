

# 📄 01_databricks_asset_bundles.md

# Databricks Asset Bundles (DAB) — CI/CD & Deployment Strategy

---

## 1️⃣ What Are Databricks Asset Bundles?

Databricks Asset Bundles (DAB) are:

> A CLI-based deployment framework for Databricks resources.

They allow you to define:

* Jobs
* Pipelines (Lakeflow / DLT)
* Notebooks
* Clusters
* Permissions
* Workflows

As code.

DABs bring:

Infrastructure-as-Code to Databricks.

---

## 2️⃣ Why DAB Exists

Before DAB:

* Manual job creation in UI
* Manual pipeline updates
* No version control
* Risk of production drift
* Hard CI/CD integration

DAB solves:

* Reproducible deployments
* Environment promotion (dev → staging → prod)
* Git-based workflows
* Automated release pipelines

Professional exam tests CI/CD maturity.

---

## 3️⃣ Core CLI Commands

From your document :

```bash
databricks bundle init
databricks bundle validate
databricks bundle deploy
databricks bundle destroy
```

---

### bundle init

Creates:

* Folder structure
* databricks.yml
* Template configuration

---

### bundle validate

Checks:

* YAML syntax
* Resource definitions
* Structural correctness

Prevents deployment errors.

---

### bundle deploy

Deploys resources to target workspace.

Creates/updates:

* Jobs
* Pipelines
* Clusters
* Permissions

---

### bundle destroy

Removes deployed resources.

Used for:

* Dev cleanup
* Temporary environments

---

## 4️⃣ databricks.yml (Core File)

Central configuration file.

Defines:

* Resources
* Targets (dev / prod)
* Workspace settings
* Variables
* Permissions

Example structure:

```yaml
targets:
  dev:
    workspace:
      host: https://...
  prod:
    workspace:
      host: https://...
```

Professional nuance:
Environment configuration is isolated via targets.

---

## 5️⃣ Multi-Environment Promotion

Typical workflow:

1️⃣ Develop in dev target
2️⃣ Validate
3️⃣ Deploy to staging
4️⃣ Promote to prod

Same codebase.

Different configuration per target.

Critical for enterprise governance.

---

## 6️⃣ Resource Types Managed by DAB

DAB can deploy:

* Lakeflow Jobs
* Lakeflow Pipelines
* SQL Warehouses
* Permissions
* Clusters

This centralizes infrastructure definition.

---

## 7️⃣ Permissions & Governance

Permissions can be declared in bundle.

Avoids:

* Manual UI permission grants
* Drift between environments
* Inconsistent access models

CI/CD + governance combined.

---

## 8️⃣ Git Integration

DAB works best with:

* Git repositories
* Pull requests
* Branch promotion
* CI automation

Workflow:

Developer commits → CI runs validate → deploy to test → approve → deploy prod.

Professional exam may test:

Why CI/CD is preferred over manual deployment.

---

## 9️⃣ Cost & Risk Benefits

DAB reduces:

* Human error
* Production drift
* Misconfigured jobs
* Accidental cluster changes

Enables:

* Reproducible infrastructure
* Auditability
* Dev/Prod isolation

---

## 🔟 Professional Exam Traps

* DAB enables Infrastructure-as-Code.
* bundle validate prevents bad deployments.
* Multiple targets allow environment separation.
* DAB integrates with CI/CD.
* Avoid manual prod edits.
* Permissions can be defined in bundle.
* DAB manages jobs and pipelines.

---

## 🧠 Deep Understanding Check

1. Why is Infrastructure-as-Code important for Databricks?
2. Why should production not be edited manually?
3. Why are multiple targets critical?
4. Why is validate important before deploy?
5. How does DAB improve governance?

---

# 🔥 Strategic Perspective

Your repository now covers:

* Delta internals
* Spark execution
* Streaming
* CDC & CDF
* Auto Loader
* Trigger modes
* Deletion vectors
* Lakeflow Jobs
* DLT / Pipelines
* Expectations
* DAB (CI/CD)
