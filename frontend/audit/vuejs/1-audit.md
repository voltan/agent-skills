# Vue Audit Master Skill (Entry Point)

> **Frontend Audit Suite — Enhanced for DeepSeek-V4 Flash.** Stepwise, deterministic, schema-driven.

## Purpose

Master entry point for auditing a Vue application. Orchestrates the full audit lifecycle: discovery, domain analysis (architecture, security, rendering, API, performance, infrastructure, dependencies, testing), cross-layer correlation with backend skills, and a final consolidated report.

## Scope

- Vue 3.5.x applications (Vue 2 is EOL — HIGH finding; flag for upgrade). Verify installed version first.
- SPA/CSR by default; SSR/SSG rules apply only when a rendering layer exists (meta-framework or Vite SSR).
- Covers client-side code and the Vue↔backend contract. Does NOT re-audit the backend; cross-layer findings are delegated to `backend/audit/nestjs/` and `backend/audit/mezzio/` skills.

## Framework Context

Vue 3.5: Composition API + `<script setup>`, Vite build, Vue Router, Pinia, `@vue/test-utils`, Vitest, `defineAsyncComponent`, Suspense, Teleport, render functions/JSX, `v-html` (escape-by-default interpolation). SSR only when a meta-framework (Nuxt, VitePress) or a custom Vite SSR layer is present.

**Version verification (MANDATORY before any rules apply):**
1. `npm ls vue` or read `package.json` + lockfile.
2. Compare with the current stable release on `https://vuejs.org/`.
3. Record the version in the analysis log. Apply Vue 3.5 rules; Vue 2 apps get an EOL upgrade finding and adjusted deprecated-API checks.

## Preconditions

1. Repository access (read) and inspectable git state (record commit hash; pull only if the operator approves).
2. Output directory `reports/YYYY-MM-DD/` created; master log `reports/YYYY-MM-DD/analysis-log.md` initialized.
3. Framework detection: `vue` dependency present (`package.json`), `vite.config.*`, `src/` with `main.ts`/`App.vue`, Vue Router/Pinia config.

## Audit Objectives

1. Determine the actual rendering mode (SPA, or SSR/SSG via a meta-framework) — do not assume SSR.
2. Identify exploitable security defects (client-side and cross-layer).
3. Identify performance, architecture, API-contract, infrastructure, dependency, and testing defects with evidence.
4. Produce a prioritized, evidence-based final report consistent with backend skill conventions.

## Execution Order (11 phases)

1. **Discovery** — framework detection, version verification, directory inventory, build/typecheck sanity (`vue-tsc --noEmit`, `vite build` if permitted), route map extraction (`router/`), store inventory (Pinia).
2. **Architecture** — run `frontend/audit/vuejs/4-architecture.md`.
3. **Security** — run `frontend/audit/vuejs/2-security.md`.
4. **Rendering** — run `frontend/audit/vuejs/5-rendering.md` (SPA vs SSR — conditional rules).
5. **API** — run `frontend/audit/vuejs/6-api.md` (inspect the Vue↔NestJS/Mezzio contract).
6. **Performance** — run `frontend/audit/vuejs/3-performance.md`.
7. **Infrastructure** — run `frontend/audit/vuejs/7-infrastructure.md`.
8. **Dependencies** — run `frontend/audit/vuejs/8-dependencies.md`.
9. **Testing** — run `frontend/audit/vuejs/9-testing.md`.
10. **Cross-Layer Correlation** — merge findings that depend on backend behavior; tag `Cross-Layer`; reference backend skills. Verify: router guard ⇒ backend authorization; client tenant IDs ⇒ backend tenant isolation; token handling ⇒ backend authentication; CORS/CSRF ⇒ backend policy; API contracts ⇒ backend DTO/input validation.
11. **Final Report** — write `reports/YYYY-MM-DD/vue-00-audit-summary.md` (executive summary, per-domain stats, severity distribution, prioritized roadmap), update `analysis-log.md`.

## Audit Rules

- **Progressive persistence (CRITICAL):** write every finding to disk immediately after discovery — never keep findings only in memory. Append; never overwrite prior findings.
- **Evidence first:** no finding without file/line or captured runtime behavior. Trace the data flow before reporting.
- **Version-aware:** never apply rules blindly to an unverified Vue version.
- **Rendering-aware:** apply SSR rules only where SSR actually exists.
- **Cross-layer discipline:** frontend-only mitigations are never sufficient for authorization/tenant-isolation claims.
- **No code mutation:** the audit only produces markdown reports.

## Detection Logic

- Enumerate: `src/main.ts`, `src/App.vue`, `src/router/` (routes + guards), `src/stores/` (Pinia), `src/views/`+`src/components/`, `src/composables/`, `src/api/` (API clients), `src/utils/`, `vite.config.*`, `package.json` + lockfile, `.env*`, deployment configs.
- Map routes to components, guards, and API dependencies.
- Run the domain skills in order; each produces findings in the shared schema.

## Evidence Requirements

Every finding: ID, Severity, Category, Title, Description, Impact, Affected Files (path + line), Evidence (code excerpt or captured behavior), Root Cause, Attack/Failure Scenario, Remediation, Validation, Cross-Layer Impact, References.

## Severity

`BLOCKER | CRITICAL | HIGH | MEDIUM | LOW | INFO` (mapping in `frontend/audit/README.md`). BLOCKER = release-stopping.

## False Positives

- Vue interpolation `{{ }}` escapes by default — never report as XSS.
- Vite's `node_modules/.vite` cache and `dist/` are build artifacts — do not report.
- Client-side route guards are navigation UX, not authorization.
- A Pinia store without SSR concerns is not a defect in a pure SPA.

## Remediation

Provide specific, actionable remediation per finding (e.g., replace `v-html` with sanitized/escaped output, move tokens to HttpOnly cookies, add runtime validation, add router meta-based authorization that the backend enforces).

## Validation

After remediation guidance, describe how to validate the fix (re-run the check, re-run tests, typecheck, capture behavior).

## Cross-Layer Considerations

- `Vue → NestJS`: JWT/token usage vs `backend/audit/nestjs/1-security-audit.md`; API contract vs DTO validation; database findings delegate to `backend/audit/nestjs/3-typeorm-audit.md`.
- `Vue → Mezzio`: token/session usage vs `backend/audit/mezzio/mezzio-security-vulnerability-audit.md`; input-filter contract vs Mezzio validation standards; database findings delegate to `backend/audit/mezzio/mezzio-database-layer-audit.md`.
- Caching layers: browser → CDN → proxy → API → backend (see `7-infrastructure.md`). Public caching of authenticated/tenant data is CRITICAL.

## References

- https://vuejs.org/ (current stable), https://router.vuejs.org/, https://pinia.vuejs.org/, https://vitejs.dev/
- https://github.com/vuejs/core/releases (security notices)

## Report & Log Integration

- Domain reports: `reports/YYYY-MM-DD/vue-01-architecture-review.md`, `vue-02-security-review.md`, `vue-03-rendering-review.md`, `vue-04-api-review.md`, `vue-05-performance-review.md`, `vue-06-infrastructure-review.md`, `vue-07-dependencies-review.md`, `vue-08-testing-review.md`.
- Master summary: `reports/YYYY-MM-DD/vue-00-audit-summary.md`.
- Shared log: `reports/YYYY-MM-DD/analysis-log.md` — one execution-log block per run (date, commit, branch, start/end, files analyzed, finding breakdown, resume point). Same convention as backend skills.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** Editorial context; the executing model MUST ignore this section.

This entry point mirrors the backend skills' lifecycle and the Nuxt entry point, with two Vue-specific twists: a mandatory rendering-mode determination (SPA vs SSR) so SSR rules stay conditional, and an explicit Vue-2-EOL upgrade check since many legacy codebases in the wild remain on Vue 2 despite its EOL. The `vue-NN-*` report naming keeps the shared `analysis-log.md` collision-free relative to both `backend/` and `frontend/audit/nuxtjs/` reports.
