cat > README-UE02.md <<'MD'
# UE02 — CI Pipeline with Matrix Builds, Coverage & SonarCloud (24 pts)

**Goal:** Extend your UE01 repository with a production-ready CI pipeline using GitHub Actions: build & test with Maven/Surefire, matrix builds (Windows/Ubuntu, Java 17/21), publish test reports as artifacts, integrate JaCoCo coverage and SonarCloud, fix reported issues, and add a build badge to your README.

---

## What you deliver
1. **GitHub repo link** (same repo as UE01, continued).
2. A short **PDF report** (max. 3 pages) with screenshots and brief comments:
   - Successful CI runs (matrix view).
   - Artifacts (Surefire reports) for each matrix variant.
   - SonarCloud project dashboard / PR decoration / Quality Gate status.
   - The changed code parts you fixed for Sonar issues (before/after).
   - Notes on any problems and how you solved them.
3. Updated **README.md** in the repo with a **build badge** pointing to your CI workflow.

---

## Scoring (24 pts total)

| # | Task | Points |
|---|------|--------|
| 1 | GitHub Actions workflow added (structure, triggers, minimal job) | **4** |
| 2 | Run unit tests in CI (Maven/Surefire) + upload Surefire reports | **4** |
| 3 | Matrix builds: OS (**ubuntu-latest**, **windows-latest**) × Java (**17**, **21**) with **one combination excluded** | **6** |
| 4 | JaCoCo integration (XML report generated) | **3** |
| 5 | SonarCloud integration; analysis runs on **every commit to `main`** | **3** |
| 6 | Analyze & fix SonarCloud issues (≥ 2) + document in PDF | **3** |
| 7 | Build **badge** added to README | **1** |

**Total:** 24 pts

---

## Prerequisites
- You continue with your **UE01** repo (same code base).
- **Java 17** (Temurin recommended), **Maven**, and a GitHub account.
- SonarCloud account with a project linked to your GitHub repo and a secret **`SONAR_TOKEN`** configured in the repo (**Settings → Secrets and variables → Actions**).

> ⚠️ If you perform CI-based analysis, **disable “Automatic Analysis”** in SonarCloud (Project → Administration → Analysis Method → OFF).

---

## Task 1 — Add the GitHub Actions Workflow (4 pts)

Create `.github/workflows/ci.yml` with sensible triggers and concurrency:

```yaml
name: CI
on:
  push:
    branches: [ main ]          # required check on main
    paths: [ 'pom.xml', 'src/**', '.github/workflows/**' ]
  pull_request:
    branches: [ main ]
    paths: [ 'pom.xml', 'src/**', '.github/workflows/**' ]

permissions:
  contents: read

concurrency:
  group: ci-${{ github.ref }}
  cancel-in-progress: true
