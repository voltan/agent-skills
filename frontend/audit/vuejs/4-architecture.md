# Vue Architecture Audit Skill

> **Frontend Audit Suite — Enhanced for DeepSeek-V4 Flash.** Stepwise, deterministic, schema-driven.

## Purpose

Assess the architectural health of a Vue application: Composition API usage, component/composable structure, state management, routing, dependency boundaries, TypeScript discipline, and the contract with the backend suites in `backend/audit/nestjs/` and `backend/audit/mezzio/`.

## Scope

Composition API, `<script setup>`, components, composables, state management (Pinia), Vue Router, dependency boundaries, component responsibilities, business logic, API abstraction, reusable components, shared state, coupling, circular dependencies, TypeScript architecture, maintainability, scalability.

## Framework Context

Vue 3.5: `<script setup>` is the recommended SFC style; composables (`useXxx`) encapsulate reusable logic; Pinia for global state; Vue Router with route meta + guards; `vite.config.*`; TypeScript via `vue-tsc` + `@vue/tsconfig`. Options API remains supported but is legacy-leaning for new code.

**Version verification (MANDATORY):** see `1-audit.md`.

## Preconditions

1. Repository read access; record commit hash.
2. Reports at `reports/YYYY-MM-DD/vue-01-architecture-review.md`; shared log initialized.

## Audit Objectives

1. Verify component/composable responsibilities and boundaries.
2. Verify state management scope (global vs local) and SSR-safety where SSR exists.
3. Verify routing structure and authorization wiring (guard = UX, backend = enforcement).
4. Verify TypeScript strictness and API-type discipline.
5. Verify the Vue↔backend contract alignment.

## Audit Rules

### Components & composables
1. **Business logic in components:** flag pages/components containing domain/API logic that belongs in composables/services — SRP finding; components should own presentation, composables own reusable logic.
2. **Composable hygiene:** flag composables that are not framework-independent (direct DOM/store coupling), that call each other in cycles, or that return unstable/unmemoized state causing churn — MEDIUM.
3. **Reusable components:** flag duplicated UI logic across views instead of shared components — LOW/MEDIUM; flag over-fragmentation (component-per-line) — LOW.
4. **Options vs Composition API:** flag new code using Options API where Composition API + `<script setup>` is the current recommendation — LOW (maintainability), not a defect.

### State
5. **Pinia scope misuse:** flag global stores for route-local or component-local state; flag stores holding server state without invalidation/staleness strategy; flag store-per-anything proliferation — MEDIUM.
6. **Uncontrolled shared state:** flag module-level mutable singletons outside Pinia (leaks, test pollution) — MEDIUM.
7. **SSR-safety of state:** if SSR exists (`5-rendering.md`), flag store initialization from browser-only APIs (hydration divergence) — HIGH (see `5-rendering.md`).

### Routing
8. **Route structure:** flag routes defined in one giant router file without modularization at scale; flag missing route-level code splitting (lazy routes) on heavy views — MEDIUM (see `3-performance.md`).
9. **Authorization wiring:** router guards + `meta.requiresAuth/roles` are navigation UX — missing guards on private routes is MEDIUM (exposure aid), but enforcement must be backend-side (`Cross-Layer` mandatory, never a security boundary).
10. **Route param handling:** flag route params/query used directly in API calls or sinks without validation (`2-security.md`, `6-api.md`).

### TypeScript & boundaries
11. **`any` at boundaries:** flag `any`/`as any` on API responses, route params, store state — MEDIUM; require typed DTOs.
12. **Type drift vs backend:** flag hand-written API types that drift from backend DTOs (`backend/audit/nestjs/`, `backend/audit/mezzio/`); recommend shared/generated types — MEDIUM (`Cross-Layer`).
13. **Strictness:** flag `tsconfig` strictness disabled, `@ts-ignore`/`@ts-expect-error` proliferation, `vue-tsc` not run in CI — MEDIUM.
14. **Circular dependencies:** flag import cycles between components/composables/stores — MEDIUM; verify with a dependency tool if available.

### Maintainability & scale
15. **Coupling to backend shape:** flag components tightly coupled to backend response shapes (backend change breaks UI) — extract typed API layer; MEDIUM.
16. **Scalability structure:** flag absence of feature/module organization once the codebase grows past a threshold (e.g., flat `components/` with hundreds of files) — LOW/MEDIUM; evaluate proportionally.

## Detection Logic

1. Inventory `src/`: components, composables, stores, router, api layer, utils, types.
2. Map data flows: view → composable → store/api → backend.
3. Scan for `any`, `@ts-ignore`, `as any`; compare API types to backend DTOs.
4. Detect cycles and god files (file size, multi-responsibility heuristics).
5. Cross-check against `backend/audit/nestjs/` and `backend/audit/mezzio/` contracts.

## Evidence Requirements

- File/line for each violation with the offending code.
- Import chain for cycles.
- Type mismatch: frontend type vs backend DTO snippet.

## Severity

- HIGH: SSR-safety violations (if SSR), API layer absent causing contract fragility at scale.
- MEDIUM: business logic in components, global-state misuse, cycles, type drift, `any` at boundaries.
- LOW/INFO: Options-API legacy code, minor fragmentation.

## False Positives

- A composable used once is not over-abstraction.
- Small apps legitimately skip strict layering — evaluate proportionally.
- Pinia for shared UI state (theme, toasts) is fine — only misuse is a finding.
- Not every component must use `<script setup>` — only new/dominant patterns matter.

## Remediation

- Extract business/API logic into typed composables/services; keep components presentational.
- Scope state: local state in components, shared state in Pinia with invalidation.
- Add route meta + guards as UX, enforce authorization backend-side.
- Generate/shared API types from backend contracts; run `vue-tsc` in CI.

## Validation

Verify with typecheck, dependency graph, and a review of the extracted layers; confirm tests pass.

## Cross-Layer Considerations

- API layer must mirror backend contracts: NestJS DTOs (`backend/audit/nestjs/1-security-audit.md`, `backend/audit/nestjs/2-performance-audit.md`), Mezzio input filters (`backend/audit/mezzio/mezzio-engineering-compliance-scorecard.md`).
- Domain naming should align with backend bounded contexts.
- Store/backend cache coherence (e.g., Redis-backed sessions) — flag drift risk.

## References

- https://vuejs.org/guide/reusability/composables, https://pinia.vuejs.org/, https://router.vuejs.org/
- https://vuejs.org/guide/typescript/overview

## Report & Log Integration

Progressive persistence to `reports/YYYY-MM-DD/vue-01-architecture-review.md`; shared log block; shared finding schema.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** Editorial context; the executing model MUST ignore this section.

Vue architecture audits frequently oscillate between over-policing (flagging every composable) and under-policing (missing contract drift). This skill anchors on the three things that actually degrade Vue apps — misplaced logic, state-scope abuse, and type drift against the backend — while keeping proportionality rules for small codebases. The router-guard rule states the frontend/backend enforcement split explicitly, matching the cross-layer doctrine of the backend security skills.
