# UE04 -- CI/CD Security Scanning with Trivy and Grype

## Overview

In this exercise you will extend your existing CI/CD pipeline with
**security scanning** using two industry-grade tools:

-   **Trivy** (Aqua Security)\
-   **Grype** (Anchore)

You will configure **both scanners** in your GitHub Actions pipeline,
generate **downloadable reports**, intentionally introduce
vulnerabilities, and experiment with **exit codes** to build your own
**quality gates**.

Maximum points: **24**

------------------------------------------------------------------------

## 🎯 Learning Objectives

After completing this exercise, you will be able to:

-   Integrate vulnerability scanners into a GitHub Actions pipeline\
-   Run Trivy and Grype locally and in CI\
-   Generate downloadable security reports as build artifacts\
-   Introduce intentional CVEs into a container image\
-   Configure severity thresholds and exit codes\
-   Build meaningful **security quality gates**\
-   Compare scan results between Trivy and Grype

------------------------------------------------------------------------

## 📝 Task Description

### 1. Integrate **Grype** into Your GitHub Actions Pipeline

-   Install Grype in your CI pipeline\

-   Scan your Docker image using:

    ``` bash
    grype <your-image> -o json
    ```

-   Generate a **JSON report**\

-   Upload this report as a **downloadable GitHub Artifact**

### 2. Integrate **Trivy** into Your GitHub Actions Pipeline

-   Run Trivy with:

    ``` bash
    trivy image <your-image> -o json
    ```

-   Produce a **JSON report**\

-   Upload the report as an Artifact\

-   Ensure both scanners run successfully in CI

### 3. Run **both scanners locally**

You must demonstrate that:

-   You installed Trivy and Grype locally\
-   You scanned your local Docker image\
-   You captured screenshots of your local scans\
-   You can compare local vs. CI scan results

Screenshots must appear in your PDF documentation.

### 4. Intentionally Introduce Vulnerabilities

Modify your project so that **at least two CVEs** are detected, for
example:

-   Add a known vulnerable library (e.g., Log4j 2.14.1)\
-   Use an outdated base image\
-   Install extra Linux packages (curl, wget, ...)\
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

## 📦 Deliverables

### **1. Link to your GitHub repository**

Containing all workflow changes.

### **2. A PDF documenting your results**

The PDF must include:

-   Screenshots of **local Trivy scans**\
-   Screenshots of **local Grype scans**\
-   Screenshots of **GitHub Actions artifacts**\
-   Explanation of how you introduced vulnerabilities\
-   Explanation of exit-code experiments\
-   Summary (5--10 sentences)

------------------------------------------------------------------------

## 🧪 Suggested Commands (Local)

### Install Grype

``` bash
curl -sSfL https://raw.githubusercontent.com/anchore/grype/main/install.sh | sudo sh -s -- -b /usr/local/bin
```

### Install Trivy

``` bash
brew install trivy       # macOS
sudo apt install trivy   # Ubuntu
```

### Local Scanning

``` bash
trivy image local/app:dev
grype local/app:dev
```

------------------------------------------------------------------------

## 🏆 Detailed Points Breakdown (24 points total)

  -------------------------------------------------------------------------------
  Category            Sub-Category         Detailed Criteria          Points
  ------------------- -------------------- -------------------------- -----------
  **1. Grype          Installation         Grype installed correctly  1
  Integration                              in CI                      
  (CI/CD)**                                                           

                      Image Scan           Image scanned              2
                                           automatically in GitHub    
                                           Actions                    

                      Report Generation    JSON report generated      1

                      Artifact Upload      Report downloadable under  2
                                           "Artifacts"                

  **➡️ Sum Grype**                                                    **6**

  **2. Trivy          Installation         Trivy installed correctly  1
  Integration                              in CI                      
  (CI/CD)**                                                           

                      Image Scan           Image scanned in GitHub    2
                                           Actions                    

                      Report Generation    JSON report generated      1

                      Artifact Upload      Report downloadable        2

  **➡️ Sum Trivy**                                                    **6**

  **3. Intentional    CVE Injection        At least 2 vulnerabilities 2
  Vulnerabilities**                        introduced                 

                      Detection            Both Grype and Trivy       2
                                           detect the issues          

  **➡️ Sum                                                            **4**
  Vulnerabilities**                                                   

  **4. Exit Code /    Trivy Experiments    Proper exit code &         2
  Quality Gate                             severity usage             
  Experiments**                                                       

                      Grype Experiments    Threshold logic tested     2

  **➡️ Sum Exit                                                       **4**
  Codes**                                                             

  **5. PDF            Screenshots          Local + CI evidence        2
  Documentation**                          included                   

                      Summary              Clear explanation of       2
                                           results                    

  **➡️ Sum                                                            **4**
  Documentation**                                                     

  **TOTAL**                                                           **24**
  -------------------------------------------------------------------------------

------------------------------------------------------------------------

## ✔️ Submission

Submit **two items**:

1.  **GitHub Repository URL**\
2.  **PDF File** with screenshots + explanations

Good luck --- and have fun breaking your own build! 🔐🚀
