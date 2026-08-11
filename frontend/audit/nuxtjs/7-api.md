# Nuxt API Integration Audit Skill

> **Frontend Audit Suite — Enhanced for DeepSeek-V4 Flash.** Stepwise, deterministic, schema-driven.

## Purpose

Audit how the Nuxt application talks to backend APIs — transport choice, authentication/credentials handling, error and race handling, response validation, and the contract with the backend suites in `backend/audit/nestjs/` and `backend/audit/mezzio/`.

## Scope

`$fetch`/`ofetch`, `useFetch`, `useAsyncData`, Axios, GraphQL, REST, WebSockets, SSE, custom API clients, authentication, credentials, cookies, CSRF, CORS, authorization, timeout, retries, backoff, cancellation, duplicate requests, race conditions, error handling, response validation, runtime validation, API contracts, API versioning, pagination, filtering, sorting, caching, stale data.

## Framework Context

Nuxt 4: `$fetch` (ofetch-based, available server+client), `useFetch`/`useAsyncData` (SSR-aware wrappers), `server/api` or `server/routes` as BFF (backend-for-frontend) proxies, Nitro `server` code can call backends with server-side secrets safely. GraphQL via modules; WebSocket/SSE via plugins/nitro.

**Version verification (MANDATORY):** see `1-audit.md`.

## Preconditions

1. Repository read access; record commit hash.
2. Reports at `reports/YYYY-MM-DD/nuxt-04-api-review.md`; shared log initialized.
3. Backend contract visibility: `backend/audit/nestjs/` and/or `backend/audit/mezzio/` present for `Cross-Layer` verification.

## Audit Objectives

1. Verify authentication/credentials are handled safely (tokens, cookies, headers) across SSR and client calls.
2. Verify error handling, timeouts, retries, cancellation, and race-condition handling.
3. Verify response runtime validation against the backend contract (DTOs / input filters).
4. Verify API-call deduplication, caching, and stale-data behavior.
5. Inspect the explicit Nuxt→NestJS and Nuxt→Mezzio contracts.

## Audit Rules

### Transport & credentials
1. **Credential handling:** flag tokens embedded in query strings (leak into logs/history — HIGH); flag bearer tokens in `localStorage`-bound stores (see `2-security.md`); verify cookies flow correctly (`credentials: 'include'` where cookies are used) and that SSR `$fetch` calls forward cookies to the backend when needed.
2. **BFF pattern:** verify the `server/api` BFF layer forwards only necessary data and never leaks server secrets to the client; flag a BFF that exposes raw backend responses including internal fields (e.g., DB ids, internal URLs, password hashes).
3. **Base URLs & env:** flag hardcoded backend URLs, missing `NUXT_PUBLIC_*`/`runtimeConfig.public` base URL configuration; flag internal service URLs leaked into client calls (SSRF surface, `2-security.md`).

### Robustness
4. **Timeouts:** flag `$fetch`/`useFetch` calls without timeout on external endpoints — hanging requests block UI/SSR; require timeout config.
5. **Retries/backoff:** flag missing retry with exponential backoff for idempotent GETs to unstable backends; flag retries on non-idempotent POSTs (duplicate side effects) without idempotency keys.
6. **Cancellation:** flag requests not cancelled on unmount/navigation (stale responses overwriting state, wasted bandwidth); use `AbortController`/`signal`.
7. **Race conditions:** flag last-write-wins state updates from out-of-order responses (no request-sequence protection); flag `watch`-triggered fetches racing with initial fetch.
8. **Duplicate requests:** flag identical concurrent requests without deduplication (`useFetch` dedupes; raw `$fetch` does not) — see `3-performance.md`.
9. **Error handling:** flag swallowed errors (`catch {}`), unhandled promise rejections in API code, missing error states in UI; flag exposing raw backend error messages/stack traces to the UI (information disclosure, `2-security.md`).

### Validation & contract
10. **Response runtime validation:** TypeScript types do NOT validate runtime data. For untrusted API responses: flag missing runtime validation when response shape drives security decisions or UI logic (use Zod/Valibot at the boundary where warranted). `Cross-Layer` with backend DTOs.
11. **`any`/unsafe casts:** flag `as any`, `as SomeType` on API responses without runtime checks.
12. **Contract drift:** compare frontend types/endpoints with backend: NestJS DTOs (`backend/audit/nestjs/`), Mezzio input filters (`backend/audit/mezzio/`). Flag mismatched fields, missing fields, wrong error shapes, version drift. Recommend shared/generated types (OpenAPI).
13. **API versioning:** flag mixed/unversioned API usage where the backend versions; flag breaking-change risk in the contract.
14. **Pagination/filter/sort:** flag frontends fetching full collections instead of using backend pagination/filter/sort (`Cross-Layer` with backend DB skills); flag missing page-size caps.

### Caching & staleness
15. **Stale data:** flag cached API data without invalidation (stale user/session data displayed after backend change); `getCachedData`/SWR misuse on authenticated data (CRITICAL if public cache — see `5-ssr.md`/`2-security.md`).
16. **SSR/client duplication:** flag data fetched both server- and client-side with divergent results.

### Realtime
17. **WebSocket/SSE:** flag missing auth on WebSocket/SSE connections, missing reconnection/backoff, unhandled message-shape validation, and missing origin checks (`Cross-Layer`).

## Detection Logic

1. Grep data-access layer: `$fetch`, `useFetch`, `useAsyncData`, `axios`, `fetch(`, GraphQL clients, WebSocket/SSE setup.
2. Extract endpoint inventory and map to backend routes in `backend/audit/nestjs/` and `backend/audit/mezzio/`.
3. For each call site: evaluate auth, timeout, retry, cancellation, error handling, validation.
4. Compare frontend payload/types against backend DTOs/input filters.
5. Check cache usage (`getCachedData`, SWR, stores) for staleness/auth issues.

## Evidence Requirements

- File/line of each call site with the relevant options.
- The backend contract definition (DTO/input filter) vs the frontend type — diff excerpt.
- Captured runtime behavior where relevant (hanging request, wrong status handling).

## Severity

- CRITICAL: token in query string; BFF leaking secrets; public cache of authenticated API data.
- HIGH: missing runtime validation on security-relevant responses; contract drift causing auth/authorization misbehavior; swallowed auth errors.
- MEDIUM: missing timeouts/retries/cancellation, race conditions, duplicate requests, stale-data issues.
- LOW/INFO: missing versioning discipline, minor error-state gaps.

## False Positives

- `useFetch` without explicit dedupe key is fine — Nuxt dedupes by URL+params by default.
- Missing runtime validation is not automatically a finding — it matters where response data drives security decisions or untrusted input reaches sinks.
- GraphQL without persisted queries is not inherently vulnerable — evaluate authorization depth `Cross-Layer`.

## Remediation

- Centralize API access in a typed client with default timeout/retry/error handling.
- Add runtime validation (Zod/Valibot) at trust boundaries; generate types from the backend contract.
- Implement cancellation and race protection in data composables.
- Route private/authenticated calls through the BFF with cookies; never expose secrets client-side.

## Validation

Re-run the affected flows after fixes; verify timeout/retry/cancellation behavior, re-check validation failures, re-verify the contract diff is resolved.

## Cross-Layer Considerations

- Nuxt → NestJS: verify against `backend/audit/nestjs/1-security-audit.md` (auth), `backend/audit/nestjs/2-performance-audit.md` (API perf), DTO validation standards.
- Nuxt → Mezzio: verify against `backend/audit/mezzio/mezzio-security-vulnerability-audit.md` (auth/validation), `backend/audit/mezzio/mezzio-engineering-compliance-scorecard.md` (validation domain).
- CORS/CSRF: frontend behavior must match backend policy — `Cross-Layer` mandatory.

## References

- https://nuxt.com/docs/4.x/recipes/data-fetching, https://nuxt.com/docs/api/composables/use-fetch
- https://github.com/unjs/ofetch, https://zod.dev/ (runtime validation)

## Report & Log Integration

Progressive persistence to `reports/YYYY-MM-DD/nuxt-04-api-review.md`; shared log block; shared finding schema.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** Editorial context; the executing model MUST ignore this section.

The contract-first design (explicit Nuxt→NestJS and Nuxt→Mezzio inspection) is what makes this skill useful in this repository — API audits that ignore the actual backend will miss contract drift, the highest-leverage defect class here. The runtime-validation rules encode the "TypeScript types do not validate runtime data" doctrine explicitly because it is the single most common frontend misconception. Robustness rules (timeout/retry/cancellation/race) are included because they are cheap to verify and frequently absent.
