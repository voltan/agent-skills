# Vue API Integration Audit Skill

> **Frontend Audit Suite — Enhanced for DeepSeek-V4 Flash.** Stepwise, deterministic, schema-driven.

## Purpose

Audit how the Vue application integrates with backend APIs — clients, authentication/credentials, robustness (timeout/retry/cancellation/races), caching, response validation, and the contract with the backend suites in `backend/audit/nestjs/` and `backend/audit/mezzio/`.

## Scope

REST, GraphQL, Axios, fetch, WebSockets, SSE, API clients, authentication, authorization, credentials, cookies, CSRF, CORS, timeout, retry, cancellation, race conditions, caching, API contracts, runtime validation, error handling.

## Framework Context

Vue 3.5 SPAs typically use Axios or fetch (often wrapped in an API client module); GraphQL via Apollo/urql; Pinia stores hold fetched state. No SSR-specific data APIs (those are Nuxt's `useFetch`/`useAsyncData`; in a plain SPA, fetching happens in `onMounted`/composables/guards). Cookies flow depends on credentials mode; CSRF depends on the backend.

**Version verification (MANDATORY):** see `1-audit.md`.

## Preconditions

1. Repository read access; record commit hash.
2. Reports at `reports/YYYY-MM-DD/vue-04-api-review.md`; shared log initialized.
3. Backend contract visibility: `backend/audit/nestjs/` and/or `backend/audit/mezzio/` present for `Cross-Layer` verification.

## Audit Objectives

1. Verify credentials/auth are handled safely across requests (tokens, cookies, headers).
2. Verify robustness: timeouts, retries, cancellation, race-condition protection.
3. Verify response runtime validation against backend contracts.
4. Verify caching/staleness and deduplication.
5. Inspect the explicit Vue→NestJS and Vue→Mezzio contracts.

## Audit Rules

### Transport & credentials
1. **Credential handling:** flag tokens in query strings (HIGH — leak into logs/history); flag bearer tokens in localStorage-bound stores (see `2-security.md`); verify cookies flow correctly (`withCredentials`/`credentials: 'include'`) for cookie-auth apps; verify axios interceptors add auth headers consistently.
2. **Base URLs & env:** flag hardcoded backend URLs; flag `VITE_*`-based API base URLs with no validation; flag internal service URLs exposed client-side (`2-security.md`).
3. **Auth failure handling:** flag missing handling of 401 (silent failure, no redirect/logout), infinite retry loops on 401 — MEDIUM.

### Robustness
4. **Timeouts:** flag API calls without timeout (hanging UI) — MEDIUM; require explicit timeouts on all external calls.
5. **Retries/backoff:** flag missing retry+backoff for idempotent calls to unstable endpoints; flag blind retries on non-idempotent writes (duplicate side effects) without idempotency keys — MEDIUM.
6. **Cancellation:** flag requests not cancelled on unmount (stale responses overwriting state, races on component reuse) — use `AbortController`/`signal`; MEDIUM.
7. **Race conditions:** flag last-write-wins updates from out-of-order responses (no sequence guard); flag refresh-token races (concurrent 401 → multiple refresh calls) — MEDIUM/HIGH.
8. **Duplicate requests:** flag identical concurrent requests without dedup (SPA pattern: same endpoint fetched by multiple components) — MEDIUM; consider a dedup layer (see `3-performance.md`).
9. **Error handling:** flag swallowed errors (`catch {}`), missing error states, raw backend error messages rendered to UI (information disclosure — MEDIUM, `Cross-Layer`).

### Validation & contract
10. **Runtime validation:** TypeScript types do not validate runtime data. Flag missing runtime validation on untrusted responses where shape drives security or critical UI decisions — MEDIUM/HIGH (`Cross-Layer`).
11. **`any`/unsafe casts:** flag `as any`/`as Type` on API responses without runtime checks — MEDIUM.
12. **Contract drift:** compare frontend types/endpoints with backend: NestJS DTOs (`backend/audit/nestjs/`), Mezzio input filters (`backend/audit/mezzio/`). Flag mismatches in fields, error shapes, status codes — MEDIUM/HIGH for auth-related contracts.
13. **API versioning:** flag mixed/unversioned usage where backend versions; flag breaking-change risk — LOW/MEDIUM.
14. **Pagination/filter/sort:** flag full-collection fetches where backend supports pagination/filtering (`Cross-Layer` with backend DB skills) — MEDIUM.

### Caching & staleness
15. **Stale data:** flag cached store state without invalidation (stale user/session/profile after backend change) — MEDIUM; flag cache-after-logout leakage (data of previous user visible) — HIGH (`Cross-Layer`).
16. **Cache headers:** flag clients relying on browser HTTP cache for authenticated endpoints (backend must set `private`/`no-store` — `Cross-Layer`).

### Realtime
17. **WebSocket/SSE:** flag unauthenticated WS/SSE connections, missing reconnection/backoff, missing message-shape validation, missing origin checks (`Cross-Layer`).

## Detection Logic

1. Inventory API layer: axios instances, fetch wrappers, GraphQL clients, WS/SSE setup.
2. Extract endpoint inventory; map to backend routes in `backend/audit/nestjs/` and `backend/audit/mezzio/`.
3. For each call site: auth, timeout, retry, cancellation, error handling, validation.
4. Compare payloads/types against backend DTOs/input filters.
5. Check store invalidation on auth state changes.

## Evidence Requirements

- File/line of each call site with relevant options.
- Contract diff excerpt (frontend type vs backend DTO/input filter).
- Race/cancellation: the offending async flow code.

## Severity

- CRITICAL: token in query string; cache-after-logout leakage.
- HIGH: missing runtime validation on security-relevant responses; contract drift breaking auth; refresh-token races.
- MEDIUM: missing timeouts/retries/cancellation, stale-data issues, swallowed auth errors.
- LOW/INFO: versioning hygiene, minor error-state gaps.

## False Positives

- Absence of retries is not always a defect — evaluate endpoint stability and idempotency.
- Runtime validation is not required on every response — only where shape drives security/critical logic.
- Axios interceptors centralizing auth are good — only direct scattered calls with duplicated auth are findings.

## Remediation

- Centralize API access in a typed client with defaults (timeout, retry policy, error normalization).
- Add runtime validation (Zod/Valibot) at trust boundaries; generate types from backend contracts.
- Implement cancellation, dedup, and race protection in composables/stores.
- Invalidate cached state on logout/session events.

## Validation

Re-run affected flows; verify timeout/retry/cancellation behavior; re-check validation failures; re-verify the contract diff is resolved.

## Cross-Layer Considerations

- Vue → NestJS: verify against `backend/audit/nestjs/1-security-audit.md` (auth), `backend/audit/nestjs/2-performance-audit.md` (API perf), DTO standards.
- Vue → Mezzio: verify against `backend/audit/mezzio/mezzio-security-vulnerability-audit.md` (auth/validation), `backend/audit/mezzio/mezzio-engineering-compliance-scorecard.md` (validation domain).
- CORS/CSRF: frontend behavior must match backend policy — `Cross-Layer` mandatory.

## References

- https://axios-http.com/docs (interceptors), https://vuejs.org/guide/reusability/composables (data composables)
- https://zod.dev/ (runtime validation), https://swagger.io/specification/ (API contracts)

## Report & Log Integration

Progressive persistence to `reports/YYYY-MM-DD/vue-04-api-review.md`; shared log block; shared finding schema.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** Editorial context; the executing model MUST ignore this section.

Like its Nuxt counterpart, this skill is contract-first: the explicit Vue→NestJS and Vue→Mezzio inspection is the primary differentiator, since contract drift is the highest-leverage defect class in a repository with real backends. The SPA-specific robustness rules (cancellation on unmount, last-write-wins races, refresh-token storms) target failure modes that are endemic to plain-Vue data layers and absent from framework-managed fetching (Nuxt dedupes, an SPA must do it manually).
