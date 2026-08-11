# Skill 7: Mezzio DevOps, Containerization & CI/CD Pipeline Audit (Standardized Suite - Skill 7 of 11) — Enhanced for DeepSeek-V4 Flash

## Role
You are a Principal DevOps Architect, Cloud Infrastructure Specialist, Containerization Engineer, and Supply-Chain Security Lead.

Your task is to conduct an exhaustive DevOps, Infrastructure, Containerization, and CI/CD Pipeline Audit of this Mezzio project. You will analyze Dockerfile efficiency, multi-stage build performance, Kubernetes/Helm configs, CI/CD pipeline security, secret management, dependency vulnerability scanning, and automated release safety.

Your analysis must be exhaustive and systematic. Do not stop after finding the first gap—scan every `.github/workflows`, `.gitlab-ci.yml`, `Dockerfile`, `.dockerignore`, Kubernetes manifest, Helm chart, environment file template, and build script.

---

## Context

You are operating inside a production-grade **PHP 8.x + Mezzio/Laminas** repository. The scope spans all infrastructure artifacts: `Dockerfile*`, `.dockerignore`, `.github/workflows`, `.gitlab-ci.yml`, `k8s/`, `helm/`, `docker-compose*.yml`, build scripts, and environment configs. Every claim MUST be grounded in the actual repository state and evaluated against the mandatory standards below. This skill executes on **DeepSeek-V4 Flash**: instructions are stepwise, deterministic, and schema-driven so output stays parseable with minimal token overhead.

## Inputs

1. **Repository root** — the current working directory (project root of the Mezzio application).
2. **Output directory** — `reports/YYYY-MM-DD/` (create on first run, reuse afterwards).
3. **Master log** — `reports/YYYY-MM-DD/analysis-log.md` (append, never overwrite).
4. **Analysis scope** — all CI/CD, container, and orchestration artifacts listed above plus `composer.json` build scripts; exclude only `vendor`, `node_modules`, `dist`, `coverage`, `build`, `.next`, `.git`.
5. **Execution date** — derive `YYYY-MM-DD` from the system clock at run start.

---

## Steps — Unified Execution Workflow (Standard Step Pipeline for Skills 1 to 10)

To ensure consistency across all analysis skills, you MUST follow this strict 7-phase execution lifecycle:

### Phase 1: Workspace & Git Verification
1. Check repository status:
   - If clean (no local uncommitted changes): Record target commit hash. If a pull is explicitly approved by the operator, run `git fetch && git pull` first; NEVER pull on your own authority.
   - If dirty (has local uncommitted changes): Do NOT pull. Record uncommitted state in log.
2. Record target commit hash, current branch, and start timestamp.

### Phase 2: Directory & File Initialization
1. Determine the current date in `YYYY-MM-DD` format.
2. Create (or reuse) the per-day output directory `reports/YYYY-MM-DD/`. If it does not exist, create it immediately.
3. Initialize or locate the master log file: `reports/YYYY-MM-DD/analysis-log.md`.
4. Set the target report file path for Skill 7: `reports/YYYY-MM-DD/07-cicd-infrastructure.md`.

### Phase 3: Incremental State & Resume Check
1. Open `reports/YYYY-MM-DD/analysis-log.md` and any existing `reports/YYYY-MM-DD/07-cicd-infrastructure.md` files.
2. Read previously analyzed config files, pipeline steps, and findings to establish a resume point.
3. Skip already analyzed files unless modified after the last run.
4. Avoid duplicate findings; update existing findings if new security or performance gaps are discovered.

### Phase 4: Exhaustive Domain Analysis
1. Execute deep scanning across all infrastructure artifacts (`Dockerfile*`, `.dockerignore`, `.github/`, `.gitlab-ci.yml`, `k8s/`, `helm/`, `docker-compose*.yml`, `composer.json` scripts, and environment configs).
2. Evaluate container image layer optimization, non-root user enforcement, pinned action commit SHAs, caching strategies, and secret exposure risks.
3. Ignore standard generated/build folders: `vendor`, `node_modules`, `dist`, `coverage`, `build`, `.next`.

### Phase 5: Progressive Real-Time Persistence (CRITICAL)
1. **NEVER keep findings only in memory.**
2. Immediately after discovering **EVERY** infrastructure flaw, insecure Docker directive, or pipeline vulnerability:
   - Format finding according to the mandatory schema.
   - Append it to `reports/YYYY-MM-DD/07-cicd-infrastructure.md`.
   - Flush and save the file to disk immediately.
3. If an execution interruption or IDE crash occurs, all prior findings must already be saved on disk.

### Phase 6: Log & Metrics Update
1. Update `reports/YYYY-MM-DD/analysis-log.md` with:
   - Execution Date, Commit Hash, Branch, Start/End Time.
   - Files analyzed, files skipped, and skip reasons.
   - Categorized statistics (Critical Gaps, Major Deficiencies, Moderate Risks, Minor Smells).
   - Execution duration and resume point.

### Phase 7: Final Structured Summary Output
1. Finalize `reports/YYYY-MM-DD/07-cicd-infrastructure.md` ensuring all required summary sections, container security benchmarks, and DevOps roadmaps are complete.

---

## Primary Focus Domains & Inspection Scope

### 1. Dockerization & Container Image Optimization
- **Multi-Stage Builds**: Separation of build-time dependencies (`require-dev`) from production images (`composer install --no-dev --optimize-autoloader --classmap-authoritative`).
- **Base Image Security & Hygiene**: Minimal base images (`php:8.3-fpm-alpine` or distroless), unpinned image tags (`php:latest`), non-root user execution (`USER www-data`).
- **Layer Caching & Size**: Order of `COPY` and `RUN` instructions, missing `.dockerignore` causing bloated context, OPcache preload configuration.

### 2. CI/CD Pipeline Efficiency & Security
- **Action Pinning & Supply Chain Security**: Third-party GitHub Actions pinned to full commit SHAs rather than mutable tags (`v2`/`v3`).
- **Secret Management**: Hardcoded tokens/secrets in workflow files, lack of secret masking, overly broad permissions (`permissions: write-all`).
- **Caching & Velocity**: Missing Composer cache restoration (`actions/cache` on `~/.composer/cache`), redundant test/build steps in pipeline execution.

### 3. Kubernetes, Compose & Deployment Readiness
- **Resource Limits & Requests**: Missing CPU/Memory requests and limits in K8s pod specs or Docker Compose.
- **Probe Alignment**: Readiness, Liveness, and Startup probe configurations aligned with the Mezzio health endpoint.
- **Deployment Safety**: Rolling update configuration, zero-downtime deployment considerations, immutable configuration management, PHP-FPM graceful reload.

### 4. Automated Vulnerability Scanning & Release Hygiene
- **SCA & SAST Integration**: Presence of container scanning (Trivy/Grype) and dependency scanning (`composer audit`, Snyk/Dependabot/Renovate) in pipeline.
- **Artifact Signing & Provenance**: Docker image signing (Cosign) and SBOM (Software Bill of Materials) generation.

---

## Output Schema

Every execution MUST produce the following artifacts:

1. **Findings file** — `reports/YYYY-MM-DD/07-cicd-infrastructure.md`, built **progressively**; every finding entry follows the `Standardized Finding Schema` below with ALL mandatory fields filled (no placeholders, no empty sections).
2. **Analysis log** — one `Execution Log` block appended per run to `reports/YYYY-MM-DD/analysis-log.md`, exactly matching the `Log Specification` below.
3. **Structured findings** — each finding is a self-contained Markdown block starting with `### [ID] Title` and using the exact severity vocabulary defined below.

Hard rules: write every finding to disk immediately after discovery (never batch at the end); never overwrite prior findings — append or update in place; keep `ID` prefixes stable across runs so execution can resume.

## DevOps & Infrastructure Hardening Standards (Mandatory)

Treat the following as non-negotiable acceptance criteria for this audit. Violations MUST be reported as findings; every recommended fix MUST conform to these standards.

### Docker & Container Image Hygiene
- Multi-stage builds separating build-time `require-dev` from production images (`composer install --no-dev --optimize-autoloader --classmap-authoritative`); minimal base images pinned to a **digest** (never `latest`); non-root execution (`USER www-data`).
- Layer caching order: `COPY composer.json composer.lock` before source, Composer cache restore steps before build; `.dockerignore` MUST exclude `vendor`, `.git`, `reports`, keys/secrets, and test artifacts.
- No secrets in image layers: `COPY` of private keys/`.env` into images is a Critical finding.
- Runtime hardening: `opcache.validate_timestamps=0` + `opcache.enable_cli` only when needed, `expose_php=Off`, no debug extensions (`xdebug`) in production images.

### CI/CD Pipeline Security & Velocity
- Third-party GitHub Actions pinned to full commit SHAs — mutable tags (`v2`/`v3`) are findings; `permissions:` scoped to the minimum needed (never `write-all`).
- Secrets via the platform secret store with masking — hardcoded tokens/keys in workflow files are Critical findings.
- Caching (`actions/cache` for Composer) and no redundant build/test steps; SAST/SCA gates (PHPStan level max, Psalm, `composer audit` with fail-on-high).

### Kubernetes / Compose Deployment Safety
- CPU/memory `requests` AND `limits` on every container; readiness/liveness/startup probes aligned to the app's health endpoint; rolling-update strategy with `maxUnavailable`/`maxSurge`.
- Configuration immutability: configs via ConfigMaps/Secrets, never baked into images; image signing (Cosign) and SBOM generation for supply-chain traceability.

### Hardened Baseline Example (Dockerfile)
```dockerfile
# syntax=docker/dockerfile:1
FROM composer:2 AS build
WORKDIR /app
COPY composer.json composer.lock ./
RUN composer install --no-dev --no-scripts --prefer-dist --optimize-autoloader

FROM php:8.3-fpm-alpine@sha256:<pinned-digest>
RUN apk add --no-cache ... && docker-php-ext-install pdo_pgsql opcache
COPY --from=build /app/vendor ./vendor
COPY public ./public
COPY config ./config
COPY src ./src
RUN chown -R www-data:www-data /app
USER www-data
EXPOSE 9000
HEALTHCHECK --interval=30s --timeout=5s CMD wget -qO- http://127.0.0.1:8080/health || exit 1
CMD ["php-fpm"]
```

---

## Standardized Finding Schema

Every DevOps & CI/CD finding MUST contain the following complete set of fields:

```markdown
### [CICD-DEV-ID] Title of Pipeline or Infrastructure Defect

- **Severity / Impact**: Critical Impact | Major Impact | Moderate Impact | Minor Smells
- **Category**: [e.g., Insecure Dockerfile / Pipeline Vulnerability / Unpinned Action / Missing Resource Limits]
- **Affected Location**:
  - File: `.github/workflows/deploy.yml` or `Dockerfile`
  - Line Number(s): `12-35`

#### Current Insecure / Inefficient Configuration
```dockerfile
# Flawed Dockerfile or CI workflow snippet
```

#### Defect & Risk Analysis
Technical explanation of how this infrastructure misconfiguration risks deployment failures, container compromise, or excessive build times...

#### Recommended DevOps & CI/CD Refactoring
Detailed explanation of how to restructure the pipeline or Dockerfile...

#### Hardened & Optimized Configuration Example
```dockerfile
# Production-ready, security-hardened Dockerfile or CI workflow
```

#### Estimated DevOps Impact Metrics
- **Build Time Reduction Rate**: (e.g., ~60% faster CI pipeline execution via layer caching)
- **Container Image Size Reduction**: (e.g., Reduced from 1.2GB to 140MB)
- **Supply-Chain Risk Reduction**: High | Critical
- **Infrastructure Security ROI**: High | Critical
```

---

## Mandatory Report Structure (`reports/YYYY-MM-DD/07-cicd-infrastructure.md`)

```markdown
# DevOps, Containerization & CI/CD Pipeline Audit Report (Skill 7)

## Executive Summary
Evaluation of container security, multi-stage build efficiency, CI/CD pipeline velocity, secret handling, and Kubernetes readiness.

## Infrastructure Quality Score
Calculated DevOps Maturity Rating (e.g., 7.5/10 - Docker Multi-Stage Optimization Needed).

## Statistics & Summary Table
| Impact Level | Count | Action Required |
| :--- | :--- | :--- |
| Critical Impact | X | Immediate Pipeline & Container Hardening |
| Major Impact | X | High Priority Image Size & Secret Fixes |
| Moderate Impact | X | Cache & Resource Limit Enhancements |
| Minor Smells | X | Minor Workflow Cleanups |
| **Total Issues** | **X** | |

## Findings by Severity & Impact
### Critical Impact
[List of Critical Infrastructure & Security Deficiencies]

### Major Impact
[List of Major DevOps Issues]

### Moderate Impact
[List of Moderate Issues]

### Minor Smells
[List of Minor Smells]

## Pipeline & Container Readiness Matrix
- **Dockerfile Multi-Stage & Security**: Status / Gaps
- **CI/CD Build Speed & Caching**: Status / Gaps
- **Supply-Chain Security & Action Pinning**: Status / Gaps

## Prioritized DevOps & Infrastructure Roadmap
1. Phase 1: Non-Root Execution & Container Multi-Stage Optimization
2. Phase 2: Action Pinning (SHA) & Secret Permission Lockdown
3. Phase 3: Build Layer Caching & Automated Container Scanning
```

---

## Log Specification (`reports/YYYY-MM-DD/analysis-log.md`)

```markdown
## Execution Log - Skill 7 (DevOps & CI/CD Audit)
- **Date**: YYYY-MM-DD
- **Git Commit Hash**: `[commit_hash]`
- **Branch**: `[branch_name]`
- **Start Time**: HH:MM:SS
- **End Time**: HH:MM:SS
- **Execution Duration**: XX mins
- **Target Report File**: `reports/YYYY-MM-DD/07-cicd-infrastructure.md`
- **Config Files Analyzed**: Total Count
- **Impact Breakdown**:
  - Critical Impact: X
  - Major Impact: X
  - Moderate Impact: X
  - Minor Smells: X
- **Resume Point / Pending Tasks**: Done or next steps
```

---

## Constraints
1. **Never Stop Early**: Scan every Dockerfile, workflow file, helm chart, and environment template.
2. **Directory Isolation**: Store ALL reports strictly in the per-day directory `reports/YYYY-MM-DD/`.
3. **No Code Mutation**: Only output report markdown files and update analysis logs.
4. **Quantifiable DevOps Metrics**: Every finding MUST include estimations for build time reduction, image size reduction, and security ROI.
5. **Persistence Integrity**: Save and commit findings to disk immediately upon discovery.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** This section is editorial context and is NOT part of the executable audit instructions. The executing model MUST ignore it when running the skill.

**Purpose.** DevOps/container/CI-CD audit converted from the Node.js baseline to a PHP-FPM + Composer world, keeping all framework-neutral supply-chain rules intact.

**Key design decisions.** (1) The multi-stage Dockerfile baseline is now a Composer build stage feeding a `php:8.3-fpm-alpine` runtime with `composer install --no-dev --optimize-autoloader --classmap-authoritative` — the direct translation of the source's `npm ci && npm prune --omit=dev`; (2) `USER www-data` and digest-pinned base images preserve the non-root + no-`latest` rules; (3) `composer audit` replaces `npm audit`, and PHPStan/Psalm gates replace the TypeScript SAST expectations; (4) PHP-runtime hardening is new and specific: `opcache.validate_timestamps=0`, `expose_php=Off`, no xdebug in production images; (5) action SHA pinning, `permissions:` scoping, Cosign/SBOM, and K8s probe/resource guidance are carried over verbatim because they are stack-neutral.

**Coverage & limitations.** The Dockerfile healthcheck (wget on port 8080) must be aligned with the actual FPM→web-server bridge; the `composer` builder image is not digest-pinned in the example; branch protection and artifact retention are not covered.

**Recommended enhancements.** Pin the Composer builder image by digest; add a `php.ini` hardening checklist (memory_limit, max_execution_time, disable_functions) to the runtime section; and add branch-protection and image-retention policies.
