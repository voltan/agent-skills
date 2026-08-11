# Frontend Audit Skills (Nuxt & Vue)

A flat, framework-specific audit skill system for modern production-grade Nuxt and Vue applications, designed to complement the backend audit skills in `backend/audit/mezzio/` and `backend/audit/nestjs/`.

## Purpose

Provide reusable, evidence-based AI auditing skills that detect concrete security vulnerabilities, architectural weaknesses, performance problems, rendering/SSR defects, SEO failures, API integration issues, infrastructure risks, dependency risks, testing gaps, and cross-layer frontend/backend failures.

These are **skills**, not documentation. Each `.md` file is a complete, self-contained audit methodology an AI coding agent (optimized for DeepSeek-V4 Flash: stepwise, deterministic, schema-driven) can load and execute against a repository.

## Framework Coverage

| Directory | Framework | Current stable guidance |
| :--- | :--- | :--- |
| `frontend/audit/nuxtjs/` | Nuxt (Universal / SSR / SSG / CSR / hybrid via route rules, Nitro server) | Nuxt 4.x (Nuxt 3 is end-of-life; Nuxt 5 in development) |
| `frontend/audit/vuejs/` | Vue (SPA / CSR, optional SSR/SSG via Vite SSR or a meta-framework) | Vue 3.5.x (Vue 2 is end-of-life) |

> **Version policy:** never assume a version. Every skill instructs the audit agent to verify the installed framework version at audit start (`nuxt --version`, `npm ls vue`, `package.json`/lockfile) and against current official documentation before applying framework-specific rules. Deprecated and legacy APIs detected during the audit are reported as findings.

## Structure (flat — no category subdirectories)

```text
frontend/audit/
├── README.md
├── nuxtjs/
│   ├── README.md
│   ├── 1-audit.md          # Nuxt master entry point & execution order
│   ├── 2-security.md
│   ├── 3-performance.md
│   ├── 4-architecture.md
│   ├── 5-ssr.md
│   ├── 6-seo.md
│   ├── 7-api.md
│   ├── 8-infrastructure.md
│   ├── 9-dependencies.md
│   └── 10-testing.md
└── vuejs/
    ├── README.md
    ├── 1-audit.md          # Vue master entry point & execution order
    ├── 2-security.md
    ├── 3-performance.md
    ├── 4-architecture.md
    ├── 5-rendering.md
    ├── 6-api.md
    ├── 7-infrastructure.md
    ├── 8-dependencies.md
    └── 9-testing.md
```

Every framework skill is a direct `.md` file inside its framework directory. No `security/`, `performance/`, `api/` etc. subdirectories exist — an AI agent can discover and load a framework's full audit surface in one directory listing.

## Execution Order

- **Nuxt:** Discovery → Architecture → Security → SSR → API → Performance → SEO → Infrastructure → Dependencies → Testing → Cross-Layer Correlation → Final Report (see `frontend/audit/nuxtjs/1-audit.md`).
- **Vue:** Discovery → Architecture → Security → Rendering → API → Performance → Infrastructure → Dependencies → Testing → Cross-Layer Correlation → Final Report (see `frontend/audit/vuejs/1-audit.md`).

## Relationship with Backend Skills

This system is deliberately consistent with the backend audit skills:

- **Methodology:** same progressive, disk-persistent execution lifecycle — findings are written to disk immediately, never kept only in memory; a shared `reports/YYYY-MM-DD/analysis-log.md` records every run for incremental resume.
- **Finding schema:** same field model as backend skills (ID, Severity, Category, Title, Description, Impact, Affected Files, Evidence, Root Cause, Attack/Failure Scenario, Remediation, Validation, Cross-Layer Impact, References), with OWASP mapping where applicable.
- **Severity model:** `CRITICAL | HIGH | MEDIUM | LOW | INFO`, plus `BLOCKER` for release-stopping findings. For cross-suite consolidation, this maps onto the backend vocabulary: CRITICAL↔Critical, HIGH↔High/Major, MEDIUM↔Medium/Moderate, LOW↔Low/Minor, INFO↔Informational.
- **Cross-layer auditing:** frontend findings that depend on backend behavior are tagged `Cross-Layer` and reference the relevant backend skill (`backend/audit/nestjs/` for NestJS targets, `backend/audit/mezzio/` for Mezzio/Laminas targets). A frontend guard is never treated as authorization; tenant selection is never treated as a security boundary.
- **Reporting:** frontend reports are written to `reports/YYYY-MM-DD/` with framework-prefixed names (e.g., `nuxt-02-security-review.md`, `vue-02-security-review.md`) so they never collide with backend report names (`01-*`…`10-*`).

## Severity Model

- **BLOCKER** — release-stopping: exploitable RCE/SSRF/command injection via server-side code, secret leakage in client bundles, authentication/authorization bypass.
- **CRITICAL** — exploitable without preconditions; cross-tenant or cross-user data exposure; stored/reflected XSS reachable from attacker-controlled input.
- **HIGH** — exploitable with conditions; token in `localStorage` in an XSS-reachable app; unsafe ISR/SWR caching of authenticated data; missing auth on a private route's data.
- **MEDIUM** — defense-in-depth gaps; missing security headers; missing runtime validation on untrusted API responses; hydration mismatches causing visible defects.
- **LOW** — hygiene: unused dependencies, minor cache misconfig, missing `hreflang`.
- **INFO** — observations and recommendations without a concrete defect.

## Evidence Requirements

No finding without evidence. When source code is available, every finding must include file, line, and a concrete code/behavior excerpt:

```text
File: pages/account.vue
Line: 42
Evidence: const token = localStorage.getItem('access_token')
Finding: Authentication token is readable by JavaScript and can be exfiltrated via a successful XSS.
Severity: HIGH
```

Generic, unanchored findings are not permitted. For behavioral findings (headers, caching, SSR output), capture the effective runtime behavior (e.g., actual `curl -I` response headers), not just source configuration.

## Cross-Layer Auditing

Both framework skill sets explicitly model the frontend/backend security boundary:

```text
Frontend route guard       →  Backend authorization       (guard is never sufficient)
Frontend tenant ID         →  Backend tenant isolation    (tenant selection is not a boundary)
Frontend token handling    →  Backend authentication
Frontend CORS usage        →  Backend CORS policy
Frontend CSRF behavior     →  Backend CSRF protection
Frontend API contract      →  NestJS DTO / Mezzio input-filter validation
```

When a finding depends on backend behavior, tag it `Cross-Layer` and reference the relevant backend skill file (`backend/audit/nestjs/1-security-audit.md`, `backend/audit/mezzio/mezzio-security-vulnerability-audit.md`, `backend/audit/nestjs/3-typeorm-audit.md`, `backend/audit/mezzio/mezzio-database-layer-audit.md`, etc.).
