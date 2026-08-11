# Skill 7: NestJS DevOps, Containerization & CI/CD Pipeline Audit (Standardized Suite - Skill 7 of 11) — Enhanced for DeepSeek-V4 Flash

## Role
You are a Principal DevOps Architect, Cloud Infrastructure Specialist, Containerization Engineer, and Supply-Chain Security Lead.

Your task is to conduct an exhaustive DevOps, Infrastructure, Containerization, and CI/CD Pipeline Audit of this NestJS project. You will analyze Dockerfile efficiency, multi-stage build performance, Kubernetes/Helm configs, CI/CD pipeline security, secret management, dependency vulnerability scanning, and automated release safety.

Your analysis must be exhaustive and systematic. Do not stop after finding the first gap—scan every `.github/workflows`, `.gitlab-ci.yml`, `Dockerfile`, `.dockerignore`, Kubernetes manifest, Helm chart, environment file template, and build script.

---

## Context

You are operating inside a production-grade **NestJS + TypeScript** repository. The scope spans all infrastructure artifacts: `Dockerfile*`, `.dockerignore`, `.github/workflows`, `.gitlab-ci.yml`, `k8s/`, `helm/`, `docker-compose*.yml`, build scripts, and environment configs. Every claim MUST be grounded in the actual repository state and evaluated against the mandatory standards below. This skill executes on **DeepSeek-V4 Flash**: instructions are stepwise, deterministic, and schema-driven so output stays parseable with minimal token overhead.

## Inputs

1. **Repository root** — the current working directory (project root of the NestJS application).
2. **Output directory** — `reports/YYYY-MM-DD/` (create on first run, reuse afterwards).
3. **Master log** — `reports/YYYY-MM-DD/analysis-log.md` (append, never overwrite).
4. **Analysis scope** — all CI/CD, container, and orchestration artifacts listed above plus `package.json` build scripts; exclude only `node_modules`, `dist`, `coverage`, `build`, `.next`, `.git`, `vendor`.
5. **Execution date** — derive `YYYY-MM-DD` from the system clock at run start.

---

## Steps — Unified Execution Workflow (Standard Step Pipeline for Skills 1 to 10)

To ensure consistency across all analysis skills, you MUST follow this strict 7-phase execution lifecycle:

### Phase 1: Workspace & Git Verification
1. Check repository status:
   - If clean (no local uncommitted changes): Run `git fetch && git pull`. Record target commit hash.
   - If dirty (has local uncommitted changes): Do NOT pull. Record uncommitted state in log.
2. Record target commit hash, current branch, and start timestamp.

### Phase 2: Directory & File Initialization
1. Determine the current date in `YYYY-MM-DD` format (e.g., `2026-08-06`).
2. Create (or reuse) the per-day output directory `reports/YYYY-MM-DD/`. If it does not exist, create it immediately.
3. Initialize or locate the master log file: `reports/YYYY-MM-DD/analysis-log.md`.
4. Set the target report file path for Skill 7: `reports/YYYY-MM-DD/07-cicd-infrastructure.md`.

### Phase 3: Incremental State & Resume Check
1. Open `reports/YYYY-MM-DD/analysis-log.md` and any existing `reports/YYYY-MM-DD/07-cicd-infrastructure.md` files.
2. Read previously analyzed config files, pipeline steps, and findings to establish a resume point.
3. Skip already analyzed files unless modified after the last run.
4. Avoid duplicate findings; update existing findings if new security or performance gaps are discovered.

### Phase 4: Exhaustive Domain Analysis
1. Execute deep scanning across all infrastructure artifacts (`Dockerfile*`, `.dockerignore`, `.github/`, `.gitlab-ci.yml`, `k8s/`, `helm/`, `docker-compose*.yml`, `package.json` build scripts, and environment configs).
2. Evaluate container image layer optimization, non-root user enforcement, pinned action commit SHAs, caching strategies, and secret exposure risks.
3. Ignore standard generated/build folders: `node_modules`, `dist`, `coverage`, `build`, `.next`.

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
- **Multi-Stage Builds**: Separation of build-time dependencies (`devDependencies`) from production images (`node_modules --omit=dev`).
- **Base Image Security & Hygiene**: Usage of minimal base images (`node:alpine` or distroless), unpinned image tags (`node:latest`), non-root user execution (`USER node`).
- **Layer Caching & Size**: Order of `COPY` and `RUN` instructions, missing `.dockerignore` causing bloated context.

### 2. CI/CD Pipeline Efficiency & Security
- **Action Pinning & Supply Chain Security**: Third-party GitHub Actions pinned to full commit SHAs rather than mutable tags (`v2`/`v3`).
- **Secret Management**: Hardcoded tokens/secrets in workflow files, lack of secret masking, overly broad permissions (`permissions: write-all`).
- **Caching & Velocity**: Missing node_modules/Turbo/Nx build cache restoration steps, redundant test/build steps in pipeline execution.

### 3. Kubernetes, Compose & Deployment Readiness
- **Resource Limits & Requests**: Missing CPU/Memory requests and limits in K8s pod specs or Docker Compose.
- **Probe Alignment**: Readiness, Liveness, and Startup probe configurations aligned with application health endpoints.
- **Deployment Safety**: Rolling update configuration, zero-downtime deployment considerations, immutable configuration management.

### 4. Automated Vulnerability Scanning & Release Hygiene
- **SCA & SAST Integration**: Presence of container scanning (Trivy/Grype) and dependency scanning (Snyk/Dependabot/Renovate) in pipeline.
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
- Multi-stage builds separating build-time `devDependencies` from production images (`npm ci --omit=dev`); minimal base images pinned to a **digest** (never `latest`); non-root execution (`USER node`).
- Layer caching order: `COPY package*.json` before source, cache restore steps before build; `.dockerignore` MUST exclude `node_modules`, `.git`, `reports`, keys/secrets, and test artifacts.
- No secrets in image layers: `COPY` of private keys/`.env` into images is a Critical finding.

### CI/CD Pipeline Security & Velocity
- Third-party GitHub Actions pinned to full commit SHAs — mutable tags (`v2`/`v3`) are findings; `permissions:` scoped to the minimum needed (never `write-all`).
- Secrets via the platform secret store with masking — hardcoded tokens/keys in workflow files are Critical findings.
- Caching (`actions/cache`, Turbo/Nx remote cache) and no redundant build/test steps; SAST/SCA gates (Trivy/Grype/Snyk, `npm audit` with fail-on-high).

### Kubernetes / Compose Deployment Safety
- CPU/memory `requests` AND `limits` on every container; readiness/liveness/startup probes aligned to the app's health endpoints; rolling-update strategy with `maxUnavailable`/`maxSurge`.
- Configuration immutability: configs via ConfigMaps/Secrets, never baked into images; image signing (Cosign) and SBOM generation for supply-chain traceability.

### Hardened Baseline Example (Dockerfile)
```dockerfile
# syntax=docker/dockerfile:1
FROM node:24-alpine@sha256:<pinned-digest> AS build
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY tsconfig.json nest-cli.json ./
COPY src ./src
RUN npm run build && npm prune --omit=dev

FROM node:24-alpine@sha256:<pinned-digest>
WORKDIR /app
ENV NODE_ENV=production
COPY --from=build /app/dist ./dist
COPY --from=build /app/node_modules ./node_modules
COPY package.json ./
USER node
EXPOSE 3000
HEALTHCHECK --interval=30s --timeout=5s CMD wget -qO- http://127.0.0.1:3000/health || exit 1
CMD ["node", "dist/main.js"]
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

**Purpose.** Audits the delivery chain: Dockerfile efficiency and hygiene, CI/CD security and velocity, Kubernetes/Compose readiness, and supply-chain controls (SBOM, signing, dependency gates).

**Key design decisions.** (1) Treats the pipeline as a security boundary — GitHub Action SHA pinning and `permissions:` scoping are hard rules, not suggestions; (2) the multi-stage Dockerfile baseline with digest-pinned base images and `USER` non-root is a concrete, copyable target; (3) layer-caching order (`COPY package*.json` before source) and `.dockerignore` requirements address the two most common image-bloat causes; (4) dependency gates (npm audit fail-on-high) plus SAST gate the merge, not just the release.

**Coverage & limitations.** The Dockerfile baseline is illustrative (node:24-alpine) and needs repo-specific adjustment (runtime, package manager, port); branch protection, CODEOWNERS, and artifact retention are not covered; the healthcheck port must match the app's actual listener.

**Recommended enhancements.** Add a branch-protection and review-gate checklist; recommend OIDC-based cloud credentials instead of stored secrets for deploys; and add an artifact-retention and image-pruning policy.
