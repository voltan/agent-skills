# Nuxt Testing & QA Audit Skill

> **Frontend Audit Suite — Enhanced for DeepSeek-V4 Flash.** Stepwise, deterministic, schema-driven.

## Purpose

Audit the Nuxt test suite across all layers — unit, component, integration, E2E, SSR/hydration, API, security, accessibility, performance, and contract tests — with special attention to security-critical flows.

## Scope

Unit tests, component tests, integration tests, E2E tests, SSR tests, hydration tests, API tests, authentication tests, authorization tests, security tests, accessibility tests, performance tests, contract tests. Test tooling: Vitest, `@vue/test-utils`, `@nuxt/test-utils`, Playwright, Cypress.

## Framework Context

Nuxt 4: `@nuxt/test-utils` (vitest environment + E2E via Playwright), Vitest, `@vue/test-utils`, `@vue/test-utils` + `happy-dom`/`jsdom`, Playwright/Cypress for E2E. SSR behavior must be tested with the Nuxt environment (or a running app), not a plain DOM environment.

**Version verification (MANDATORY):** see `1-audit.md`.

## Preconditions

1. Repository read access; record commit hash.
2. Reports at `reports/YYYY-MM-DD/nuxt-09-testing-review.md`; shared log initialized.
3. Test config present (`vitest.config.ts`, `nuxt.config.ts` test section, `playwright.config.ts`).

## Audit Objectives

1. Verify test coverage is meaningful (logic branches and error paths, not line-coverage vanity).
2. Verify SSR/hydration behavior is actually tested (not just DOM unit tests).
3. Verify security-critical flows (auth, authorization, tenant isolation, token handling) have dedicated tests.
4. Verify E2E/contract tests match the backend contract.
5. Verify flakiness controls (deterministic time, seeded data, isolated state).

## Audit Rules

### Coverage & isolation
1. **Meaningful coverage:** flag core logic (composables, stores, auth flows, API clients, route middleware) with no tests or only happy-path tests; flag untested error/branch paths — MEDIUM/HIGH for auth and data logic.
2. **Test isolation:** flag shared mutable state across tests, missing cleanup, tests depending on execution order, real network/backend calls in unit tests (should be mocked) — MEDIUM.
3. **Typed fixtures:** flag untyped inline literals in test data that drift from source types — LOW/MEDIUM; use typed factories.

### SSR & hydration
4. **SSR tests missing:** flag Nuxt apps where SSR behavior (data fetching during SSR, payload, hydration) has no tests (`@nuxt/test-utils` SSR environment or E2E against a running server) — MEDIUM/HIGH, especially for auth and caching behavior.
5. **Hydration-mismatch tests:** verify at least one test catches hydration mismatches (E2E console error assertions or SSR-vs-client render comparison) — MEDIUM.
6. **Client-only behavior:** verify `<ClientOnly>`/browser-only-API code has tests that run client-side — LOW/MEDIUM.

### API & contract
7. **API contract tests:** verify tests assert response shapes against the backend contract (DTOs/input filters); missing contract tests where backend types exist — MEDIUM. `Cross-Layer`.
8. **Mock accuracy:** flag mocks that diverge from real backend behavior (e.g., mocked success only, wrong error shapes) — MEDIUM.
9. **Error-path tests:** verify API error handling (timeouts, 4xx/5xx, network failure) is tested — MEDIUM.

### Security
10. **Security-critical flows:** verify dedicated tests for: authentication (login/logout/token expiry), authorization (role-based route/data access), tenant isolation (user A cannot access user B data), CSRF behavior, token storage. Missing security tests on these flows — HIGH.
11. **XSS regression tests:** verify tests for any `v-html`/sanitization boundary (render, assert no script execution) — MEDIUM.
12. **Security scan integration:** note whether a DAST/security scan (e.g., OWASP ZAP, automated header checks) exists in CI — MEDIUM if absent on public deployments.

### E2E & a11y & perf
13. **E2E coverage:** flag critical user journeys (auth, checkout, tenant switch, core CRUD) without E2E coverage — MEDIUM.
14. **Accessibility:** flag missing a11y tests/checks (axe) on key flows — MEDIUM (a11y is a quality gate, not security).
15. **Performance tests:** flag absence of performance regression checks (bundle-size budgets, CWV thresholds) for critical routes — LOW/MEDIUM; see `3-performance.md`.
16. **Flakiness:** flag tests using `sleep()`, real clocks without fake timers, unseeded randomness, date-dependent logic without time control — MEDIUM (flaky tests erode the suite).

## Detection Logic

1. Inventory test files and map them to source modules.
2. Identify untested critical modules (auth, tenant logic, API layer, middleware, SSR-sensitive components).
3. Verify SSR/hydration coverage exists (`@nuxt/test-utils`, E2E).
4. Inspect test config for isolation and deterministic-time controls.
5. Check CI for test + coverage + audit gates (see `9-dependencies.md` for audit gates).

## Evidence Requirements

- Test file/line for present coverage; source file/line for missing coverage.
- The specific flow with no test (e.g., "refresh-token rotation has no test").
- Mock code that diverges from the backend contract.

## Severity

- HIGH: security-critical flows untested; SSR auth behavior untested.
- MEDIUM: missing contract/SSR/error-path coverage, flaky-test patterns, missing E2E on core journeys.
- LOW/INFO: coverage gaps in non-critical UI, untyped fixtures, minor a11y gaps.

## False Positives

- Not every component needs a test — evaluate logic-bearing code, not templates-only components.
- Coverage percentages alone are not findings; missing branches and flows are.
- An SPA-only Nuxt app legitimately has no SSR tests — apply SSR rules only when SSR is enabled (see `5-ssr.md`).

## Remediation

- Add typed test factories and branch tests for core logic.
- Add SSR/hydration tests via `@nuxt/test-utils`/E2E; add security-flow tests (auth, authorization, tenant isolation).
- Add contract tests aligned with backend DTOs; add CI coverage and audit gates.
- Replace sleeps with deterministic time control; isolate shared state.

## Validation

Run the suite after fixes; verify new tests fail without the fix (mutation-style check for critical flows); verify CI gates enforce coverage/audit results.

## Cross-Layer Considerations

- Contract tests must mirror backend DTOs/input filters — align with `7-api.md` and backend skills.
- Auth/tenant test scenarios depend on backend behavior — coordinate with backend QA/testing skills (`backend/audit/nestjs/5-qa-audit.md`, `backend/audit/mezzio/mezzio-qa-testing-audit.md`).

## References

- https://nuxt.com/docs/4.x/recipes/testing (or current), https://vitest.dev/, https://playwright.dev/
- https://vuejs.org/guide/scaling-up/testing (Vue testing docs)

## Report & Log Integration

Progressive persistence to `reports/YYYY-MM-DD/nuxt-09-testing-review.md`; shared log block; shared finding schema.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** Editorial context; the executing model MUST ignore this section.

The highest-value rule here is the SSR/hydration testing requirement — Nuxt's SSR behavior is its defining feature, and a test suite that only runs DOM unit tests will miss exactly the defects that hurt production (hydration mismatches, SSR auth leakage, payload bugs). Security-flow tests are elevated to HIGH deliberately, mirroring the backend QA skills' treatment of auth/tenant scenarios as first-class test subjects.
