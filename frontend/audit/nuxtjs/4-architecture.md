# Nuxt Architecture Audit Skill

> **Frontend Audit Suite — Enhanced for DeepSeek-V4 Flash.** Stepwise, deterministic, schema-driven.

## Purpose

Assess the architectural health of a Nuxt application: project structure, layering, state management, server/client boundaries, maintainability, scalability, and TypeScript architecture — and evaluate its contract with the backend suites in `backend/audit/nestjs/` and `backend/audit/mezzio/`.

## Scope

Pages, layouts, components, composables, plugins, middleware, modules, server (Nitro), runtime configuration, state management (Pinia), API abstraction, domain separation, server/client boundaries, reusable components, coupling, circular dependencies, business logic inside components, inappropriate client-side/server-side responsibilities, maintainability, scalability, TypeScript architecture.

## Framework Context

Nuxt 4 conventions: `app.vue`, `pages/`, `layouts/`, `components/` (auto-import), `composables/` (auto-import), `plugins/`, `middleware/`, `modules/`, `server/` (api/routes/middleware/plugins/utils), `nuxt.config.ts` (`runtimeConfig`, `appConfig`, `routeRules`, `imports`). Composition API + `<script setup>` is the default. Pinia via `@pinia/nuxt`.

**Version verification (MANDATORY):** see `1-audit.md`.

## Preconditions

1. Repository read access; record commit hash.
2. Reports at `reports/YYYY-MM-DD/nuxt-01-architecture-review.md`; shared log initialized.

## Audit Objectives

1. Verify layering: presentation (pages/components) ↔ application logic (composables/services) ↔ data access (API clients/repositories).
2. Verify the server/client boundary: no server secrets or heavy server work in client code; no client-only APIs on the server path.
3. Verify state management is justified and scoped; flag global-state abuse for route-local data.
4. Verify TypeScript strictness and no `any` leakage at boundaries.
5. Verify the frontend↔backend contract is explicit and versioned.

## Audit Rules

### Structure & layering
1. **Business logic in components:** flag pages/components containing domain logic that should live in composables/services/domain modules (SRP violation). Components should own presentation; composables own reusable logic; server code owns server-side concerns.
2. **API abstraction:** flag `$fetch`/`useFetch` calls scattered across components with duplicated endpoint strings; require a typed API layer (e.g., `server/api` endpoints + typed composables or an API client module) — enables contracts, error handling, and testing. Duplicated endpoint strings are a maintainability finding.
3. **Domain separation:** flag a single "god" composable/store; missing feature modularization when the app grows. Nuxt does not force modules for small apps — evaluate proportionally.
4. **Coupling/circular dependencies:** flag circular imports between composables/modules/components; Nuxt auto-imports can hide cycles — check with a dependency graph tool or import tracing.
5. **Reusable components:** flag duplicated UI logic/components instead of shared components (DRY at the component level).

### Server/client boundary
6. **Inappropriate client responsibilities:** flag heavy server work (DB queries, secrets, filesystem) attempted client-side; flag client-only code (window/localStorage) used during SSR without guards (`5-ssr.md`).
7. **Inappropriate server responsibilities:** flag presentation logic in `server/` handlers; server should expose typed API contracts, not render UI.
8. **Runtime configuration discipline:** flag `runtimeConfig` keys read in client-reachable code without classifying public vs secret (see `2-security.md`); verify `appConfig` is used for client-side config that changes per build.

### State management
9. **Pinia misuse:** flag global stores used for route-local or component-local state (creates coupling and stale state); flag stores holding server state without invalidation strategy; flag store-per-page patterns that should be composables. Flag `useState` (Nuxt) for truly global, SSR-safe shared state vs Pinia — use the right tool.
10. **SSR state divergence:** flag store state initialized from browser-only APIs (leads to hydration mismatch — see `5-ssr.md`).

### TypeScript
11. **`any` at boundaries:** flag `any`/`as any` in API response handling, route params, and store state; require typed DTOs/interfaces. `Cross-Layer`: API response types must match backend DTOs (`backend/audit/nestjs/` DTOs, `backend/audit/mezzio/` input filters).
12. **Type drift:** flag duplicated hand-written API types that drift from backend contracts; recommend generated/shared types (OpenAPI codegen) when feasible.
13. **Strictness:** flag `tsconfig` loosened flags (`strict: false`, `skipLibCheck` abuse masking errors); Nuxt's generated types (`nuxt typecheck`) should pass.

## Detection Logic

1. Inventory: `pages/`, `layouts/`, `components/`, `composables/`, `plugins/`, `middleware/`, `modules/`, `server/`, `stores/` (or `store/`), `types/`.
2. Dependency analysis: map component→composable→service→API-client flows; detect cycles and god modules.
3. Boundary scan: grep for `localStorage`/`window`/`document` in universal files; grep for `useRuntimeConfig` usage per file (classify public/secret).
4. Type scan: grep `any`, `as unknown as`, `@ts-ignore`; compare API types to backend DTO definitions.
5. Cross-check against `backend/audit/nestjs/` and `backend/audit/mezzio/` for API contract alignment (endpoints, payloads, error shapes).

## Evidence Requirements

- File/line for each layering violation with the offending code.
- Dependency cycle: the import chain.
- Boundary violation: the code + where it executes (client vs server).
- Type mismatch: frontend type vs backend DTO snippet.

## Severity

- CRITICAL: server secrets reachable from client code (architectural enabler of the security finding).
- HIGH: business logic in components at scale, missing API abstraction causing duplicated/fragile contracts, client/server boundary violations causing SSR breakage.
- MEDIUM: global state misuse, circular imports, type drift.
- LOW/INFO: naming/hygiene, minor duplication.

## False Positives

- Nuxt auto-imports (`useFoo` from `composables/`) are by design — not magic.
- A composable used in one component is not over-abstraction.
- Small apps legitimately skip modules/layers — evaluate proportionally to codebase size.
- `appConfig` vs `runtimeConfig` usage is a design choice, not inherently a finding — only when it leaks secrets or misclassifies scope.

## Remediation

- Extract business logic into typed composables/services; keep components presentational.
- Centralize API access in a typed client layer; generate/shared types from backend contracts.
- Move client-only code behind `onMounted`/`<ClientOnly>` or to `ssr: false` data calls.
- Scope state: route-local state via composables, global state via `useState`/Pinia with invalidation.

## Validation

For each architectural recommendation, describe how to verify (typecheck, dependency graph re-run, test suite, manual boundary inspection).

## Cross-Layer Considerations

- Frontend API layer must mirror backend contracts: NestJS DTOs (`backend/audit/nestjs/1-security-audit.md`, `backend/audit/nestjs/2-performance-audit.md`) and Mezzio input filters/validation (`backend/audit/mezzio/mezzio-security-vulnerability-audit.md`, `backend/audit/mezzio/mezzio-engineering-compliance-scorecard.md`).
- Domain boundaries on the frontend should not fight backend bounded contexts — coordinate domain naming.
- State synchronization between frontend store and backend cache (e.g., Redis-backed) — flag drift risk.

## References

- https://nuxt.com/docs/4.x/getting-started/directory-structure, https://nuxt.com/docs/guide/concepts/auto-imports
- https://pinia.vuejs.org/ (state), https://www.typescriptlang.org/tsconfig (strict)
- Clean architecture / SOLID (same references as backend architecture skills)

## Report & Log Integration

Progressive persistence to `reports/YYYY-MM-DD/nuxt-01-architecture-review.md`; shared log block; shared finding schema.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** Editorial context; the executing model MUST ignore this section.

Nuxt's auto-import and directory conventions make structure almost free, so this skill focuses on the things that actually degrade: misplaced business logic, missing API abstraction, server/client boundary leaks, and type drift against the backend. The explicit "evaluate proportionally" rules prevent over-engineering complaints on small apps — a deliberate counterweight to the backend skills' stricter DDD stance, because frontend codebases at small scale legitimately differ.
