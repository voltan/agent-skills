# Vue Testing & QA Audit Skill

> **Frontend Audit Suite — Enhanced for DeepSeek-V4 Flash.** Stepwise, deterministic, schema-driven.

## Purpose

Audit the Vue test suite across all layers — unit, component, integration, E2E, security, accessibility, API contract, authentication, authorization, rendering, and performance tests — with special attention to security-critical flows.

## Scope

Unit testing, component testing, integration testing, E2E testing, security testing, accessibility testing, API contract testing, authentication testing, authorization testing, rendering testing, performance testing. Tooling: Vitest, `@vue/test-utils`, Vue Test Utils + jsdom/happy-dom, Playwright, Cypress.

## Framework Context

Vue 3.5: Vitest + `@vue/test-utils` for unit/component tests (jsdom/happy-dom environments), Playwright/Cypress for E2E, `vue-tsc` for type checking. Component tests mount SFCs; E2E runs against a built/dev app. SSR rendering tests apply only when SSR exists (`5-rendering.md`).

**Version verification (MANDATORY):** see `1-audit.md`.

## Preconditions

1. Repository read access; record commit hash.
2. Reports at `reports/YYYY-MM-DD/vue-08-testing-review.md`; shared log initialized.
3. Test config present (`vitest.config.ts`, `vite.config` test block, `playwright.config.ts`).

## Audit Objectives

1. Verify meaningful coverage of logic-bearing code (not line-coverage vanity).
2. Verify component tests cover props/emits/error states, not just happy paths.
3. Verify security-critical flows (auth, authorization, tenant isolation, token handling) have dedicated tests.
4. Verify API/contract tests match the backend contract.
5. Verify rendering tests match the app's actual rendering mode.

## Audit Rules

### Coverage & isolation
1. **Meaningful coverage:** flag core logic (stores, composables, API clients, router guards, auth flows) with no tests or only happy-path tests; flag untested error/branch paths — MEDIUM/HIGH for auth and data logic.
2. **Component tests:** flag components with complex prop/logic surfaces tested only via snapshot or no mount tests; flag missing emitted-event and error-state tests — MEDIUM.
3. **Test isolation:** flag shared mutable state across tests, missing cleanup, order-dependent tests, real network/backend calls in unit tests (should be mocked) — MEDIUM.
4. **Typed fixtures:** flag untyped inline literals drifting from source types — LOW/MEDIUM; use typed factories.

### Security
5. **Security-critical flows:** verify dedicated tests for: login/logout/token expiry, role-based access (route guard behavior + backend enforcement), tenant isolation (user A cannot see user B data), token storage behavior, 401 handling. Missing security tests → HIGH.
6. **XSS regression tests:** verify tests for any `v-html`/rich-text boundary (render, assert no script execution) — MEDIUM.
7. **Security scan integration:** note whether automated scanning (header checks, DAST) exists in CI — MEDIUM if absent on public deployments.

### API & contract
8. **API contract tests:** verify response-shape assertions match the backend contract (DTOs/input filters) — MEDIUM; `Cross-Layer`.
9. **Mock accuracy:** flag mocks that diverge from real backend behavior (success-only mocks, wrong error shapes) — MEDIUM.
10. **Error-path tests:** verify timeout/4xx/5xx/network-failure handling is tested — MEDIUM.

### E2E, rendering & a11y
11. **E2E coverage:** flag critical journeys (auth, checkout, tenant switch, core CRUD) without E2E — MEDIUM.
12. **Rendering tests:** SPA: verify mount/behavior tests exist for key views. If SSR exists (`5-rendering.md`), verify SSR/hydration behavior has tests — MEDIUM/HIGH when SSR is present.
13. **Accessibility:** flag missing a11y checks (axe) on key flows — MEDIUM.
14. **Performance tests:** flag absence of performance regression checks (bundle budgets, interaction-latency assertions) — LOW/MEDIUM (see `3-performance.md`).
15. **Flakiness:** flag `sleep()`, real clocks without fake timers, unseeded randomness, date-dependent logic — MEDIUM.

## Detection Logic

1. Inventory test files; map to source modules.
2. Identify untested critical modules (auth, tenant logic, API layer, guards, complex composables).
3. Inspect test config for isolation and deterministic time control.
4. Verify E2E and (if SSR) rendering coverage exists.
5. Check CI for test + coverage + audit gates.

## Evidence Requirements

- Test file/line for present coverage; source file/line for missing coverage.
- The specific flow with no test (e.g., "refresh-token rotation has no test").
- Mock code diverging from the backend contract.

## Severity

- HIGH: security-critical flows untested; SSR rendering untested where SSR exists.
- MEDIUM: missing contract/error-path/E2E coverage, flaky-test patterns, a11y gaps on key flows.
- LOW/INFO: coverage gaps in non-critical UI, untyped fixtures.

## False Positives

- Not every component needs a test — evaluate logic-bearing components.
- Coverage percentages alone are not findings; missing flows are.
- Pure-SPA apps legitimately have no SSR tests — apply rendering rules only where SSR exists.

## Remediation

- Add typed factories, branch tests, error-state component tests.
- Add security-flow tests (auth, authorization, tenant isolation, token storage).
- Add contract tests aligned with backend DTOs; add CI coverage and audit gates.
- Replace sleeps with deterministic time control; isolate state.

## Validation

Run the suite after fixes; verify new tests fail without the fix (mutation-style check for critical flows); verify CI gates enforce results.

## Cross-Layer Considerations

- Contract tests must mirror backend DTOs/input filters — align with `6-api.md` and backend skills.
- Auth/tenant test scenarios depend on backend behavior — coordinate with backend QA/testing skills (`backend/audit/nestjs/5-qa-audit.md`, `backend/audit/mezzio/mezzio-qa-testing-audit.md`).

## References

- https://vuejs.org/guide/scaling-up/testing, https://vitest.dev/, https://test-utils.vuejs.org/
- https://playwright.dev/, https://www.cypress.io/

## Report & Log Integration

Progressive persistence to `reports/YYYY-MM-DD/vue-08-testing-review.md`; shared log block; shared finding schema.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** Editorial context; the executing model MUST ignore this section.

The security-flow testing rule (auth, authorization, tenant isolation as first-class test subjects) is the deliberate echo of the backend QA skills' stance — in a multi-tenant repository, an untested tenant-isolation frontend flow is a HIGH finding, not a nice-to-have. The rendering-mode gate keeps SSR test expectations honest for the majority of Vue apps that are pure SPAs.
