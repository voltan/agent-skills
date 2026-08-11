# Nuxt Audit Master Skill (Entry Point)

> **Frontend Audit Suite — Enhanced for DeepSeek-V4 Flash.** Stepwise, deterministic, schema-driven.

## Purpose

Master entry point for auditing a Nuxt application. Orchestrates the full audit lifecycle: discovery, domain analysis (architecture, security, SSR, API, performance, SEO, infrastructure, dependencies, testing), cross-layer correlation with backend skills, and a final consolidated report.

## Scope

- Nuxt 4.x applications (Nuxt 3 is EOL — flag for upgrade). Verify installed version first.
- Covers client-side code, server-side Nuxt/Nitro code, and the Nuxt↔backend contract.
- Does NOT re-audit the backend; cross-layer findings are delegated to `backend/audit/nestjs/` and `backend/audit/mezzio/` skills.

## Framework Context

Nuxt 4: Vue 3.5, Vite, Nitro server engine (`server/` directory with `api/`, `routes/`, `middleware/`, `plugins/`), `runtimeConfig` + `appConfig`, route rules (SSR/CSR/ISR/SWR/prerender), `useFetch`/`useAsyncData`/`$fetch`, modules (e.g., `@nuxtjs/security`, `@pinia/nuxt`, `@nuxt/image`, `@nuxtjs/seo`).

**Version verification (MANDATORY before any rules apply):**
1. `nuxt --version` or read `package.json` + lockfile.
2. Compare with the current stable release on `https://nuxt.com/` (and `https://vuejs.org/` for the Vue core).
3. Record the version in the analysis log. Apply Nuxt-4 rules; if the app is Nuxt 3 (EOL) or Nuxt 2, flag the upgrade gap and adjust deprecated-API checks.

## Preconditions

1. Repository access (read) and a clean/inspectable git state (record commit hash; pull only if the operator approves — never pull autonomously).
2. Output directory `reports/YYYY-MM-DD/` created; master log `reports/YYYY-MM-DD/analysis-log.md` initialized.
3. Framework detection: confirm the app is Nuxt (presence of `nuxt.config.ts`, `app.vue`, `pages/`, `server/`, or a `nuxt` dependency).

## Audit Objectives

1. Determine the application's actual rendering topology per route (SSR / SSG / CSR / hybrid via route rules).
2. Identify exploitable security defects (client, server, and cross-layer).
3. Identify performance, SEO, API-contract, infrastructure, dependency, and testing defects with evidence.
4. Produce a prioritized, evidence-based final report consistent with backend skill conventions.

## Execution Order (12 phases)

1. **Discovery** — framework detection, version verification, directory inventory, build/typecheck sanity (`npx nuxt typecheck`, `npm run build` if permitted), route map extraction from `pages/`, `app.vue`, and `nuxt.config.ts`.
2. **Architecture** — run `frontend/audit/nuxtjs/4-architecture.md`.
3. **Security** — run `frontend/audit/nuxtjs/2-security.md`.
4. **SSR** — run `frontend/audit/nuxtjs/5-ssr.md`.
5. **API** — run `frontend/audit/nuxtjs/7-api.md` (inspect the Nuxt↔NestJS/Mezzio contract).
6. **Performance** — run `frontend/audit/nuxtjs/3-performance.md`.
7. **SEO** — run `frontend/audit/nuxtjs/6-seo.md`.
8. **Infrastructure** — run `frontend/audit/nuxtjs/8-infrastructure.md`.
9. **Dependencies** — run `frontend/audit/nuxtjs/9-dependencies.md`.
10. **Testing** — run `frontend/audit/nuxtjs/10-testing.md`.
11. **Cross-Layer Correlation** — merge findings that depend on backend behavior; tag `Cross-Layer`; reference backend skills (`backend/audit/nestjs/`, `backend/audit/mezzio/`). Verify: frontend guard ⇒ backend authorization; client tenant IDs ⇒ backend tenant isolation; token handling ⇒ backend authentication; CORS/CSRF ⇒ backend policy; API contracts ⇒ backend DTO/input validation.
12. **Final Report** — write `reports/YYYY-MM-DD/nuxt-00-audit-summary.md` (executive summary, per-domain stats, severity distribution, prioritized remediation roadmap), update `analysis-log.md`.

## Audit Rules

- **Progressive persistence (CRITICAL):** write every finding to disk immediately after discovery — never keep findings only in memory. Append; never overwrite prior findings.
- **Evidence first:** no finding without file/line or captured runtime behavior. Trace the data flow before reporting (see False Positives guidance in each domain skill).
- **Version-aware:** never apply rules blindly to an unverified Nuxt version.
- **Cross-layer discipline:** frontend-only mitigations are never sufficient for authorization/tenant-isolation claims — mark and escalate.
- **No code mutation:** the audit only produces markdown reports; never modify application code.

## Detection Logic

- Enumerate: `nuxt.config.ts` (runtimeConfig, routeRules, modules, nitro, app.head/security headers), `pages/` + `app.vue` (routes, middleware), `components/`, `composables/`, `plugins/`, `middleware/`, `server/` (api/routes/middleware/plugins/utils), `assets/`, `public/`, `package.json` + lockfile, `.env*`, `Dockerfile`/deployment configs.
- Map each route to its rendering strategy (routeRules/`ssr` flag/default) and its data dependencies (useFetch/useAsyncData/$fetch calls and endpoints).
- Run the domain skills in order; each produces findings in the shared schema.

## Evidence Requirements

Every finding: ID, Severity, Category, Title, Description, Impact, Affected Files (path + line), Evidence (code excerpt or captured behavior), Root Cause, Attack/Failure Scenario, Remediation, Validation, Cross-Layer Impact, References. Examples of valid evidence: the exact `runtimeConfig.public` secret exposure line; the actual `curl -I` headers from the deployed app; the `useFetch` call producing an N+1 waterfall.

## Severity

`BLOCKER | CRITICAL | HIGH | MEDIUM | LOW | INFO`. Mapping to backend vocabulary: CRITICAL↔Critical, HIGH↔High/Major, MEDIUM↔Medium/Moderate, LOW↔Low/Minor, INFO↔Informational. BLOCKER = release-stopping.

## False Positives

- Nuxt's auto-imports and virtual files (`#imports`, `.nuxt/`) are build artifacts — do not report them.
- `useHead`/`useSeoMeta` produce valid runtime tags; do not report absence of `nuxt.config.ts` head keys unless the rendered HTML lacks the tags.
- Server-only secrets accessed only inside `server/` or `nitro` code are NOT client exposures — verify the code path reaches the client bundle.
- Do not report `v-html` without tracing attacker-controlled input to a reachable sink (see `2-security.md`).

## Remediation

Provide specific, actionable remediation per finding (e.g., move a secret from `runtimeConfig` to `runtimeConfig.serverOnly`/Nitro, replace `v-html` with escaped output or DOMPurify, add route rule caching bounds, add runtime validation to API responses). Reference official Nuxt docs where applicable.

## Validation

- After remediation guidance, describe how to validate the fix (re-run the check, capture headers again, re-run tests, typecheck).
- For the audit itself: verify the report directory contains all expected per-domain report files and the summary.

## Cross-Layer Considerations

- `Nuxt → NestJS`: verify JWT/token usage against `backend/audit/nestjs/1-security-audit.md`; API contract against DTO validation; database findings delegate to `backend/audit/nestjs/3-typeorm-audit.md`.
- `Nuxt → Mezzio`: verify token/session usage against `backend/audit/mezzio/mezzio-security-vulnerability-audit.md`; input-filter contract against Mezzio validation standards; database findings delegate to `backend/audit/mezzio/mezzio-database-layer-audit.md`.
- Caching layers: browser → CDN → reverse proxy → Nitro → API → backend (see `8-infrastructure.md`). Public caching of authenticated/tenant data is CRITICAL.

## References

- https://nuxt.com/docs (current stable), https://nuxt.com/docs/4.x (v4), https://vuejs.org/, https://nitro.build/
- https://github.com/nuxt/nuxt/releases (EOL and security notices)

## Report & Log Integration

- Domain reports: `reports/YYYY-MM-DD/nuxt-01-architecture-review.md`, `nuxt-02-security-review.md`, `nuxt-03-ssr-review.md`, `nuxt-04-api-review.md`, `nuxt-05-performance-review.md`, `nuxt-06-seo-review.md`, `nuxt-07-infrastructure-review.md`, `nuxt-08-dependencies-review.md`, `nuxt-09-testing-review.md`.
- Master summary: `reports/YYYY-MM-DD/nuxt-00-audit-summary.md`.
- Shared log: `reports/YYYY-MM-DD/analysis-log.md` — one execution-log block per run (date, commit, branch, start/end, files analyzed, finding breakdown, resume point). Same convention as backend skills.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** Editorial context; the executing model MUST ignore this section.

This entry point deliberately mirrors the backend skills' 7-phase lifecycle (workspace verification, report init, incremental resume, exhaustive analysis, progressive persistence, log update, final summary) while frontloading a framework-specific Discovery phase and a mandatory version-verification precondition — the two most common failure points when a generic agent audits a Nuxt repo. The per-domain report filenames (`nuxt-NN-*`) exist so the shared `analysis-log.md` and future cross-suite consolidation (e.g., a frontend master orchestrator like the backend `11-master-consolidation-*` skills) can aggregate without colliding with `backend/` names.
