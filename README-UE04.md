# UE04 -- CI/CD Security Scanning with Trivy and Grype

## Overview

In this exercise you will extend your existing CI/CD pipeline with
**security scanning** using two industry-grade tools:

-   **Trivy** (Aqua Security)
-   **Grype** (Anchore)

You will configure **both scanners** in your GitHub Actions pipeline,
generate **downloadable reports**, intentionally introduce
vulnerabilities, and experiment with **exit codes** to build your own
**quality gates**.

Maximum points: **24**

------------------------------------------------------------------------

## Learning Objectives

After completing this exercise, you will be able to:

-   Integrate vulnerability scanners into a GitHub Actions pipeline
-   Run Trivy and Grype locally and in CI
-   Generate downloadable security reports as build artifacts
-   Introduce intentional CVEs into a container image
-   Configure severity thresholds and exit codes
-   Build meaningful **security quality gates**
-   Compare scan results between Trivy and Grype

------------------------------------------------------------------------

## Task Description

### 1. Run **both scanners locally**

You must demonstrate that:

-   You installed Trivy and Grype locally
-   You scanned your local Docker image
-   You captured screenshots of your local scans
-   You can compare local vs. CI scan results

Screenshots must appear in your PDF documentation.

### 2. Integrate **Grype** into Your GitHub Actions Pipeline

-   Install Grype in your CI pipeline
-   Scan your Docker image
-   Generate a **JSON report**
-   Upload this report as a **downloadable GitHub Artifact**

### 3. Integrate **Trivy** into Your GitHub Actions Pipeline

Install Trivy in your CI pipeline
-   Scan your Docker image
-   Generate a **JSON report**
-   Upload this report as a **downloadable GitHub Artifact**
-   Ensure both scanners run successfully in CI



### 4. Intentionally Introduce Vulnerabilities

Modify your project so that **at least two CVEs** are detected, for
example:

-   Add a known vulnerable library (e.g., Log4j 2.14.1)
-   Use an outdated base image
-   Install extra Linux packages (curl, wget, ...)
-   Add old Java/Python/Node packages

### 5. Experiment with **Exit Codes / Quality Gates**

Try out several configurations:

#### For Trivy:

-   `--severity HIGH,CRITICAL`\
-   `--exit-code 1`\
-   `--ignore-unfixed`

#### For Grype:

-   `--fail-on high`\
-   `--only-fixed`

Document what happens in each experiment.

------------------------------------------------------------------------

## Deliverables

### **1. Link to your GitHub repository**

Containing all workflow changes.

### **2. A PDF documenting your results**

The PDF must include:

-   Screenshots of **local Trivy scans**
-   Screenshots of **local Grype scans**
-   Screenshots of **GitHub Actions artifacts**
-   Explanation of how you introduced vulnerabilities
-   Explanation of exit-code experiments
-   Summary (5--10 sentences)

------------------------------------------------------------------------


## Detailed Points Breakdown (24 points total)

  ## Tasks & Points

| Section | Task | What we look for | Pts |
|--------|------|------------------|----:|
| **A. Local Security Scanning** | Install **Trivy** & run local scan | Successful installation; local scan executed; screenshot in PDF | 3 |
|  | Install **Grype** & run local scan | Successful installation; local scan executed; screenshot in PDF | 3 |
|  | Compare local results | Short observation: differences in counts, severities, scanning time | 1 |
| **B. CI Integration (GitHub Actions)** | Add **Grype** to pipeline | Correct installation + scan job; stable run | 2 |
|  | Upload **Grype JSON report** as artifact | Artifact visible & downloadable | 2 |
|  | Add **Trivy** to pipeline | Correct installation + scan job; stable run | 2 |
|  | Upload **Trivy JSON report** as artifact | Artifact visible & downloadable | 2 |
|  | Workflow quality | Good job names; proper `needs:` usage; clean structure; minimal noise | 1 |
| **C. Vulnerability Engineering** | Intentionally introduce CVEs | At least **two** real vulnerabilities created (e.g. Log4j 2.14.1 etc.) | 2 |
|  | Detection by both scanners | Trivy *and* Grype detect the CVEs; evidence provided | 2 |
| **D. Quality Gates / Exit Code Experiments** | Trivy exit-code experiments | Use of `--exit-code`, `--severity`, `--ignore-unfixed`; behavior documented and discussed | 2 |
|  | Grype exit-code experiments | Use of `--fail-on`, `--only-fixed`; behavior documented and discussed | 2 |
|  | Explanation in PDF | Clear description of tests, outcomes, and insights | 2 |
| **E. PDF Documentation** | Screenshots | Local Trivy + Grype, CI artifacts, failing/passing gates | 1 |
|  | Structure & clarity | Clean documentation; includes repo link | 1 |
| **Total** |  |  | **24** |


------------------------------------------------------------------------

## ✔️ Submission

Submit **two items**:

1.  **GitHub Repository URL**\
2.  **PDF File** with screenshots + explanations


