# Nuxt SSR / Rendering Audit Skill

> **Frontend Audit Suite — Enhanced for DeepSeek-V4 Flash.** Stepwise, deterministic, schema-driven.

## Purpose

Determine whether the selected rendering strategy (Universal SSR, CSR, SSG, hybrid via route rules) is appropriate for each route, and audit hydration correctness, SSR data/payload exposure, caching of SSR responses, and server-side execution safety.

## Scope

Universal SSR, CSR, SSG, hybrid rendering, route rules, prerendering, ISR, SWR, Nitro rendering, server routes/middleware/plugins, hydration, hydration mismatches, `<ClientOnly>`, browser-only APIs (`window`, `document`, `localStorage`, `sessionStorage`), server/client state divergence, SSR data exposure, payload exposure, authentication during SSR, private user data, caching of SSR responses, authenticated vs public SSR pages, expensive server-side API calls, blocking operations, server-side secrets.

## Framework Context

Nuxt 4 default is Universal (SSR) unless `ssr: false`; per-route control via `routeRules` (`ssr`, `isr`, `swr`, `prerender`, `crawler`, `app`). Rendering decision points: `nuxt.config.ts` `ssr`, `routeRules`, `prerender`, `nitro.prerender`; per-page via `definePageMeta`. Hydration data flows via the serialized payload (`__NUXT_DATA__` / `useNuxtApp().payload`).

**Version verification (MANDATORY):** see `1-audit.md`.

## Preconditions

1. Repository read access; record commit hash.
2. Reports at `reports/YYYY-MM-DD/nuxt-03-ssr-review.md`; shared log initialized.
3. A runnable app is strongly preferred (SSR behavior must be observed, not assumed).

## Audit Objectives

1. Produce a per-route rendering-strategy verdict (SSR / CSR / SSG / hybrid) with justification.
2. Detect hydration mismatches and browser-only API misuse.
3. Detect SSR data leakage (private data in the initial HTML/payload).
4. Detect unsafe caching of SSR responses (public cache of private/authenticated data).
5. Detect expensive/blocking server-side work in the SSR path.

## Audit Rules

### Strategy selection
1. **Strategy appropriateness per route:** for each route, evaluate: is SSR needed (indexable content, initial-load UX), is SSG/prerender possible (public, build-time data), is CSR right (authenticated app shell, dynamic dashboards)? Flag routes using SSR where prerender would serve the same content at lower cost, and routes using CSR that need indexable content (SEO impact — see `6-seo.md`). Do not assume SSR everywhere.
2. **routeRules misuse:** flag `ssr: false` on routes that need indexing; flag `prerender` on routes whose data is only available at runtime; flag `swr`/`isr` on authenticated or tenant-scoped routes (CRITICAL cache leak vector).
3. **Hybrid consistency:** verify the mix of SSR/CSR/SSG routes is coherent and documented; flag accidental global `ssr: false` disabling SSR for routes that need it.

### Hydration & client/server divergence
4. **Hydration mismatches:** flag code paths that produce different output on server vs client: `new Date()`/`Date.now()`, `Math.random()`, `window`/`document` access at render time, `localStorage` reads in `setup`, environment-dependent rendering. Each mismatch is a MEDIUM+ finding (visible flash, broken interactivity).
5. **Browser-only APIs unguarded:** `window`, `document`, `localStorage`, `sessionStorage` used in universal code without `onMounted`/`<ClientOnly>`/`import.meta.client` guard — flag with the exact usage. `import.meta.server`/`import.meta.client` guards are the accepted pattern.
6. **`<ClientOnly>`:** flag its use when the content should be server-rendered (SEO-relevant) — `<ClientOnly>` hides content from SSR; when used for genuine client-only widgets, verify no SEO-critical content inside.
7. **State divergence:** flag store/composable state initialized from browser-only values (Pinia/`useState`) causing server/client divergence.

### SSR data & payload exposure
8. **Private data in SSR payload:** for authenticated routes, flag `useAsyncData`/`useFetch` results containing private user/tenant data that get serialized into the payload — even with `ssr: false` on navigation, the first SSR render may embed data. HIGH. Mitigations: fetch client-side for private data, `ssr: false` on the call, keep payload minimal (`transform`/`pick`).
9. **Payload bloat / leakage surface:** inspect `__NUXT_DATA__` for secrets, tokens, or PII; flag unnecessary transfer of full collections (see `3-performance.md`).
10. **Authentication during SSR:** flag SSR pages that assume `useState('user')` is populated without server-side auth verification; SSR must verify identity server-side (`Cross-Layer` with backend auth), never trust client-side state alone.

### SSR caching
11. **Caching of authenticated SSR pages:** flag `swr`/`isr`/CDN caching on authenticated pages — cross-user/cross-tenant response reuse is CRITICAL. Private routes must be `no-store`/`private`.
12. **Cache keys:** flag cache keys that omit user/tenant identity; verify the full cache chain (browser → CDN → Nitro) honors auth state (see `8-infrastructure.md`).
13. **Stale auth state:** flag cached SSR responses that can outlive a logout/session revocation (stale authenticated content served from cache).

### Server-side execution
14. **Expensive server calls in SSR:** flag blocking/slow work in the SSR path: synchronous DB access, heavy computations, unbounded third-party calls in `useAsyncData` handlers — TTFB impact (see `3-performance.md`).
15. **Server secrets:** flag server-only secrets referenced in universal code (delegated severity to `2-security.md`); verify Nitro `server/` code does not serialize secrets into responses/payloads.

## Detection Logic

1. Build the route map from `pages/` + `app.vue` + `nuxt.config.ts` (ssr flag, routeRules).
2. For each route, classify: public/private, static/dynamic, indexable/not.
3. Grep universal code for browser-only APIs and non-deterministic values (`Date`, `Math.random`, `window`, `document`, `localStorage`, `sessionStorage`).
4. Inspect data calls per route (`useAsyncData`/`useFetch`): what data, what `ssr`/`server` options, what's serialized.
5. Capture the rendered HTML/payload for representative routes (curl the app, inspect `__NUXT_DATA__`).

## Evidence Requirements

- Rendering-strategy verdict per route with config/route references.
- Hydration mismatch: the code producing divergent output + observed mismatch if captured.
- Payload leak: the serialized payload excerpt containing private data + the data call that produced it.
- Cache finding: route rule + captured cache headers on an authenticated route.

## Severity

- CRITICAL: public caching of authenticated/tenant data; private data embedded in payload; stale-auth cached responses.
- HIGH: unguarded browser-only APIs causing SSR breakage, private data in SSR payload on auth routes, SSR of non-indexable content cost.
- MEDIUM: hydration mismatches, `<ClientOnly>` hiding SEO content, expensive SSR path.
- LOW/INFO: strategy micro-optimizations, missing `import.meta.client` on harmless reads.

## False Positives

- `<ClientOnly>` for genuinely client-only widgets (maps, editors) is correct — not a finding.
- `useState` with a deterministic default is fine; only browser-dependent initialization is a divergence.
- SSR of an authenticated page is not automatically leakage — verify what is actually serialized.
- `swr` on public data is a valid strategy — only unsafe when data is private/authenticated.

## Remediation

- Strategy: adjust `routeRules`/`ssr` per route with justification.
- Divergence: guard browser-only code, use `onMounted`, deterministic values (server-consistent clock via injection).
- Leakage: `ssr: false` data calls for private data, `transform`/`pick` payloads, server-side auth verification.
- Caching: `no-store` on private routes, identity-aware cache keys, revoke stale cached sessions.

## Validation

Re-render the route and re-inspect the payload/headers; confirm the divergence is gone; verify caching behavior with authenticated and anonymous requests.

## Cross-Layer Considerations

- SSR authentication must verify server-side against the backend (`Cross-Layer`, `backend/audit/nestjs/1-security-audit.md`, `backend/audit/mezzio/mezzio-security-vulnerability-audit.md`).
- Cache headers may be overridden by CDN/proxy — coordinate with `8-infrastructure.md`.
- Data shapes fetched during SSR mirror backend payloads — reference backend API/DB skills for N+1/over-fetching.

## References

- https://nuxt.com/docs/4.x/recipes/rendering (or current), https://nuxt.com/docs/api/utils/import.meta.client
- https://nuxt.com/docs/4.x/recipes/caching (route rules / ISR / SWR)

## Report & Log Integration

Progressive persistence to `reports/YYYY-MM-DD/nuxt-03-ssr-review.md`; shared log block; shared finding schema.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** Editorial context; the executing model MUST ignore this section.

The core stance — "do not assume SSR is always required" — is the deliberate opposite of the common audit bias, and the strategy-verdict-per-route rule forces evidence over habit. Hydration divergence and SSR payload leakage are Nuxt's two highest-frequency real defects, so they get dedicated rule clusters with a low tolerance for ambiguity; caching rules intentionally mirror `2-security.md` because SSR cache leaks are the single most damaging Nuxt production incident class.
