# UE04 — CI/CD Security Scanning with Trivy and Grype (24 pts)

This exercise builds on **UE03 (Docker in CI/CD)**. You will extend your existing pipeline with **security scanning** using two scanners:

- **Trivy** (Aqua Security)
- **Grype** (Anchore)

You will run both scanners locally and in GitHub Actions, generate **downloadable reports**, intentionally introduce vulnerabilities and experiment with **exit codes** to build your own **quality gates**. Submit a **PDF** (screenshots + short comments) and your **GitHub repo link**.

## Learning objectives
- Run Trivy and Grype locally and in CI.
- Integrate vulnerability scanners into a GitHub Actions pipeline.
- Generate downloadable security reports as build artifacts.
- Introduce intentional CVEs into a container image.
- Configure severity thresholds and exit codes and build **security quality gates**.
- Compare scan results between Trivy and Grype.

## Deliverables
1. **PDF** with concise screenshots and short comments:
    - Local Trivy scan and local Grype scan
    - GitHub Actions run with both report artifacts
    - How you introduced the vulnerabilities
    - Exit-code experiments (passing and failing gate)
    - Summary (5–10 sentences)

2. **GitHub repository link** (default branch up to date, containing all workflow changes)

Submit your results to eLearning until **December 11th, 2026, 23:55**

## Prerequisites
- UE03 completed: multi-stage `Dockerfile`, Docker job in the pipeline, image available as artifact
- Docker Desktop / Docker Engine installed
- Trivy and Grype installed locally (see guidance below)

## Tasks & Points
| Section | Task | What we look for | Pts |
|--------|------|------------------|----:|
| **A. Local Security Scanning** | Install **Trivy** & run local scan | Local scan of your image executed; screenshot in PDF | 2 |
|  | Install **Grype** & run local scan | Local scan of your image executed; screenshot in PDF | 2 |
|  | Compare the results | Short observation: Trivy vs. Grype (counts, severities, scanning time) and local vs. CI | 1 |
| **B. CI Integration (GitHub Actions)** | Add **Grype** to pipeline | Scan of the image built in CI; stable run | 2 |
|  | Upload **Grype JSON report** as artifact | Artifact visible & downloadable | 2 |
|  | Add **Trivy** to pipeline | Scan of the image built in CI; stable run | 2 |
|  | Upload **Trivy JSON report** as artifact | Artifact visible & downloadable | 2 |
| **C. Vulnerability Engineering** | Intentionally introduce CVEs | At least **two additional** vulnerabilities of severity HIGH or CRITICAL in the image (e.g. Log4j 2.14.1) | 2 |
|  | Detection by both scanners | Trivy *and* Grype detect them; evidence provided | 2 |
| **D. Quality Gates / Exit Code Experiments** | Trivy exit-code experiments | Use of `--exit-code`, `--severity`, `--ignore-unfixed`; behavior documented and discussed | 2 |
|  | Grype exit-code experiments | Use of `--fail-on`, `--only-fixed`; behavior documented and discussed | 2 |
|  | Explanation in PDF | Clear description of tests, outcomes and insights | 1 |
| **E. PDF Documentation** | Screenshots | Local Trivy + Grype, CI artifacts, failing/passing gates | 1 |
|  | Structure & clarity | Clean documentation; includes repo link | 1 |
|  | **Total** |  | **24** |

## Expected implementation guidance

### 1) Local scanning
- Installation: Trivy <https://trivy.dev/docs/latest/getting-started/installation/>, Grype <https://oss.anchore.com/docs/installation/grype/>. On macOS: `brew install trivy grype`.
- Build your image as in UE03 and scan it with both tools:
  ```bash
  docker build -t local/app:dev .
  trivy image local/app:dev
  grype local/app:dev
  ```
- The first scan downloads the vulnerability database and takes longer.
- Starting point: without any changes both scanners already report findings from the base image (`eclipse-temurin:17-jre`), currently only LOW and MEDIUM (Trivy 0.76.2, Grype 0.122.1, November 2026). Note the numbers as your baseline.

### 2) CI integration
- Add the scans **after** the Docker job from UE03 (`needs:`). Either scan in the same job right after the image build, or download the image artifact in a new job and `docker load` it first.
- Trivy: `aquasecurity/trivy-action@v0.36.0` with `image-ref`, `format: json`, `output: trivy-image-report.json`. Older tags of this action (e.g. `0.33.1`) no longer exist.
- Grype: `anchore/scan-action@v7.4.3`, for example:
  ```yaml
  - name: Grype image scan (JSON report)
    uses: anchore/scan-action@v7.4.3
    with:
      image: local/app:${{ steps.vars.outputs.short_sha }}
      output-format: json
      output-file: grype-image-report.json
      severity-cutoff: high
      fail-build: false
  ```
- Use the full version tag for the Grype action. The short tag `v7` currently points to an older release that installs Grype 0.110.0. That version reports additional HIGH findings in the base image, so the CI result would differ from your local scan.
- Scanner version and database date influence the result. Note both when you compare local and CI results.
- Upload both JSON files with `actions/upload-artifact@v7`. Add `if: always()` to the upload steps. Otherwise no report is uploaded when a gate fails the scan step.
- Adapt the image name to the tag you use in your Docker job.

### 3) Introducing vulnerabilities
- **A dependency in `pom.xml` alone is not enough.** The Dockerfile from UE03 copies only the application JAR into the image. A library such as Log4j 2.14.1 never reaches the image and both image scans find nothing.
- Copy the dependencies into the image as well, for example:
  ```dockerfile
  # build stage
  RUN mvn -B package dependency:copy-dependencies -DincludeScope=runtime

  # runtime stage
  COPY --from=build /app/target/*.jar /app/app.jar
  COPY --from=build /app/target/dependency/ /app/lib/
  ENTRYPOINT ["java","-cp","/app/app.jar:/app/lib/*","com.example.cicd.App"]
  ```
  `target/dependency` only exists if the project has at least one runtime dependency.
- Example dependency:
  ```xml
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.14.1</version>
  </dependency>
  ```
- Alternative: an outdated base image, e.g. `eclipse-temurin:17.0.2_8-jre`.
- Show in the PDF that the new findings appear in **both** scanners (compare with your baseline).

### 4) Exit codes / quality gates
- Trivy (CLI): `--severity HIGH,CRITICAL`, `--exit-code 1`, `--ignore-unfixed`. In `trivy-action` the inputs are `severity`, `exit-code`, `ignore-unfixed`.
- Grype (CLI): `--fail-on high`, `--only-fixed`. In the Grype action the inputs are `severity-cutoff`, `fail-build`, `only-fixed`.
- Check the exit code locally with `echo $?` (PowerShell: `$LASTEXITCODE`). Trivy returns the value given with `--exit-code`, Grype returns `2` when `--fail-on` is triggered.
- Run each gate at least once with a passing and once with a failing result, locally or in CI, and document what happens.
- **State at submission:** the default branch contains the introduced vulnerabilities, both scans and both report uploads. The pipeline run must finish (reports available as artifacts). Whether your final gate is set to fail the build or only to report is your decision; explain it in the PDF.

### Acceptance criteria (checklist)
- Local Trivy and Grype scans are documented with screenshots.
- **CI**: both scanners run on the image built in the pipeline; `trivy-image-report.json` and `grype-image-report.json` (or similar names) are downloadable artifacts.
- At least two additional HIGH/CRITICAL vulnerabilities are present in the image and detected by both scanners.
- Exit-code experiments for both scanners are documented (passing and failing).
- PDF contains the repo link and a summary.
