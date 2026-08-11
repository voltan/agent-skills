# Nuxt Performance Audit Skill

> **Frontend Audit Suite — Enhanced for DeepSeek-V4 Flash.** Stepwise, deterministic, schema-driven.

## Purpose

Detect performance defects across five distinct layers — **network**, **server** (Nuxt/Nitro), **browser**, **rendering** (SSR/hydration), and **API** — with evidence and quantified impact where possible.

## Scope

SSR performance, rendering strategy, hydration cost, bundle size, JS execution, code splitting, lazy loading, dynamic imports, component lazy loading, API waterfalls, duplicate API requests, `useFetch`/`useAsyncData`/`$fetch`, request deduplication, caching (browser/CDN/Nitro/route rules), ISR, SWR, prerendering, image optimization, fonts, third-party scripts, Core Web Vitals, TTFB, long tasks, memory usage, unnecessary client-side JavaScript.

## Framework Context

Nuxt 4: Vue 3.5, Vite, Nitro. Key mechanisms: `useFetch`/`useAsyncData` (deduplicated, SSR-aware), `$fetch`/`ofetch`, route rules (`ssr`, `isr`, `swr`, `prerender`, `crawler`), `nuxt.config.ts` (`app.head`, `nitro`, `build.analysis`, `optimization`), `@nuxt/image`, `nuxt/fonts`, `<Lazy*>` components, `definePageMeta`/`defineNuxtComponent` options.

**Version verification (MANDATORY):** see `1-audit.md`. Rules target Nuxt 4; Nuxt 3 (EOL) may lack some APIs.

## Preconditions

1. Repository read access; record commit hash.
2. Reports at `reports/YYYY-MM-DD/nuxt-05-performance-review.md`; shared log initialized.
3. Ideally: build output available (`npx nuxt build`) and a deployed or local-runnable instance to measure.

## Audit Objectives

1. Classify each finding by layer (network/server/browser/rendering/API).
2. Identify measurable regressions (bundle size, TTFB, hydration cost, waterfall depth) with evidence.
3. Verify the rendering strategy per route is appropriate (delegate depth to `5-ssr.md`).
4. Verify caching is safe and effective (delegate safety to `2-security.md`/`5-ssr.md`).

## Audit Rules

### Server & rendering
1. **SSR cost:** flag expensive work in `setup`/server path of pages (synchronous CPU loops, blocking `await` chains, heavy JSON parsing in `useAsyncData` handlers). TTFB evidence required.
2. **Rendering strategy per route:** verify SSR is justified per route; public content-heavy routes may benefit from ISR/prerender; authenticated/dynamic routes must not be cached publicly. Do not assume SSR everywhere (`5-ssr.md`).
3. **Hydration cost:** flag large payloads transferred to the client (big `useAsyncData` results, full collections) — hydrate cost correlates with payload size and component count. Flag `__NUXT_DATA__` payload bloat.
4. **Code splitting:** flag a single monolithic page bundle; verify `pages/` produce route-level chunks (Nuxt default), and that heavy third-party libs are not forced into the initial chunk (no dynamic import, `import()` in module scope).
5. **Component lazy loading:** flag heavy components (editors, charts, modals) imported eagerly; recommend `<Lazy*>` / `defineAsyncComponent` / dynamic import when above-the-fold impact is real.

### Data fetching & API
6. **Waterfalls:** detect sequential `await useFetch`/`useFetch` chains in the same page (N+1 render requests). Parallelize with `Promise.all`/multiple `useFetch` calls (Nuxt dedupes + parallelizes when called at setup top level).
7. **Duplicate requests:** flag identical `$fetch`/`useFetch` calls to the same endpoint within a render (same keys should be deduplicated; different keys for the same data = duplication). `useFetch` uses a default key from the URL+params; `$fetch` does not dedupe — flag `$fetch` in `setup`/`onMounted` that duplicates an existing `useFetch`.
8. **`useFetch` options misuse:** flag `useFetch` in `onMounted` (loses SSR data + dedup), `server: false` where server fetch is wanted, missing `key` causing re-fetch storms, `watch` triggering extra fetches.
9. **No caching of stable data:** repeated identical requests to the same endpoint without `getCachedData`/`swr`/route-rule caching — MEDIUM for hot endpoints.
10. **Client-only data access:** fetching on every client navigation data that could be server-fetched or cached.

### Network & caching
11. **Caching layers:** verify browser/CDN/proxy/Nitro cache headers and route rules: public data should be cacheable (`isr`/`swr` with sane TTL), private/authenticated data must never be cached publicly (CRITICAL — see `2-security.md`/`5-ssr.md`).
12. **ISR/SWR safety:** flag `swr`/`isr` on authenticated or tenant-scoped routes; stale-auth responses and cross-tenant cache poisoning are CRITICAL.
13. **Prerendering:** flag prerendered routes that still fetch dynamic data at runtime (defeats SSG); verify `prerender: true` routes have all data at build time.

### Assets & browser
14. **Image optimization:** flag unoptimized `<img>` (no `@nuxt/image`, no `width`/`height`, no `loading="lazy"` for below-fold, no `srcset`/provider optimization). Large hero images without dimensions cause CLS.
15. **Fonts:** flag self-hosted fonts blocking render (no `font-display: swap`, no preload, multiple weights) — use `nuxt/fonts` and `display: swap`.
16. **Third-party scripts:** flag render-blocking third-party scripts (analytics, widgets) without `defer`/`async`/`partytown`-style offloading; missing `SRI` (integrity) on external scripts is also a security note (`2-security.md`).
17. **TTFB & blocking server work:** flag high TTFB contributors in the SSR path: slow `useAsyncData` handlers (external API latency, un-bounded queries), synchronous CPU work, missing response streaming/caching. Correlate with the backend API skill when the latency is backend-owned (`Cross-Layer`).
18. **Long tasks & memory:** flag client long tasks (heavy synchronous work in event handlers, large parsing) hurting INP; flag unbounded client caches, listeners/timers/observers not cleaned up on unmount (memory leaks), and large reactive stores retained in memory.
19. **Core Web Vitals:** check LCP, INP, CLS contributors: oversized images, no preload of LCP resource, layout shift from missing dimensions, long tasks from heavy client bundles.

## Detection Logic

1. Extract route map and per-route data calls (`useFetch`/`useAsyncData`/`$fetch`).
2. Build a fetch graph per page; detect serial chains (waterfalls) and duplicate endpoints.
3. Inspect `nuxt.config.ts`: `routeRules`, `build`/`vite` config, `nitro`, `app.head`.
4. Inspect component usage: eager imports of heavy libs, `<Lazy*>` usage, dynamic imports.
5. Measure (if possible): `npx nuxi build` output sizes (`build.analysis`), TTFB on routes, initial HTML size, transferred JS (`__NUXT_DATA__`).

## Evidence Requirements

- Bundle/chunk sizes (from build output or `vite build` analysis) with file references.
- Waterfall: the exact sequence of awaited fetch calls with endpoints.
- TTFB/payload: captured measurements or the code paths that cause them (e.g., `await Promise.all` chains, heavy setup loops).
- Duplicate requests: identical endpoints fetched with different keys.

## Severity

- CRITICAL: public caching of authenticated/tenant data; render-blocking behavior causing a hard outage-class regression.
- HIGH: multi-level waterfalls on critical routes, enormous payloads hurting LCP/INP, unsafe ISR/SWR on private routes.
- MEDIUM: missing caching of stable data, unoptimized images/fonts/scripts, `$fetch` duplication.
- LOW/INFO: minor Lazy-loading opportunities, unused bundle contributors, premature-optimization notes.

## False Positives

- `useFetch` calls at setup top level are deduplicated/parallelized by Nuxt — do not report as waterfalls.
- Code-splitting absence is not a defect if the initial bundle is small and routes are few.
- SSR without hydration pessimization on non-interactive routes is not automatically wrong — measure first.
- Do not demand caching on every route; uncached dynamic routes may be correct by design.

## Remediation

- Waterfalls → hoist data calls to setup top level, `Promise.all`, or move data to layout/app-level with dedupe keys.
- Payload → select only needed fields (`pick`/`transform` in `useFetch`/`useAsyncData`), paginate.
- Bundles → dynamic imports, `Lazy` components, `nuxt.config` `optimization`/`tree-shake` settings.
- Caching → route rules (`isr`/`swr` with TTL) for public data; never for private.
- Images/fonts/scripts → `@nuxt/image`, `nuxt/fonts`, `defer`/`async` + SRI.

## Validation

Re-measure after remediation (bundle size, TTFB, waterfall depth, Lighthouse/CWV) and confirm the route's caching behavior is correct and safe.

## Cross-Layer Considerations

- API latency and missing backend pagination/filtering surface as frontend waterfalls — tag `Cross-Layer` with `backend/audit/nestjs/2-performance-audit.md` or `backend/audit/mezzio/mezzio-architecture-performance-audit.md`.
- Cache headers may be set by CDN/proxy (backend-owned) — coordinate with `8-infrastructure.md` and the backend infra skill.
- N+1 data shapes from the backend amplify payloads — reference backend database skills.

## References

- https://nuxt.com/docs/4.x/recipes/data-fetching, https://nuxt.com/docs/api/composables/use-fetch, https://nuxt.com/docs/4.x/recipes/caching
- https://web.dev/vitals (Core Web Vitals), https://nitro.build/

## Report & Log Integration

Progressive persistence to `reports/YYYY-MM-DD/nuxt-05-performance-review.md`; shared log block in `analysis-log.md`; findings in the shared schema.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** Editorial context; the executing model MUST ignore this section.

The five-layer model forces the auditor to separate symptoms (slow LCP) from causes (waterfalls vs payload vs TTFB vs client bundle). The emphasis on `useFetch` deduplication semantics and "measure first" evidence prevents the most common Nuxt performance false positives — reporting normal framework behavior (setup-level parallel fetching, route-level code splitting) as defects. Caching-safety rules intentionally overlap with `2-security.md`/`5-ssr.md`; the cross-references are deliberate so a performance finding never sidesteps the data-leak rules.
