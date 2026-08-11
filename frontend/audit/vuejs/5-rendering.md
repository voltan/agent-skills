# Vue Rendering Audit Skill

> **Frontend Audit Suite — Enhanced for DeepSeek-V4 Flash.** Stepwise, deterministic, schema-driven.

## Purpose

Audit the rendering mode and rendering behavior of a Vue application — SPA/CSR, or SSR/SSG where a rendering layer exists — including hydration, async rendering, Suspense/Teleport usage, and client/server boundaries. **Do not assume every Vue application uses SSR.**

## Scope

SPA, CSR, SSR where applicable, SSG where applicable, hydration, hydration mismatch, browser-only APIs, rendering performance, lazy rendering, async components, Suspense, Teleport, dynamic components, client/server boundaries.

## Framework Context

Vue 3.5 SPA: single client bundle, `createApp` mount, browser-only by nature. SSR/SSG only when a meta-framework (Nuxt, VitePress) or a custom Vite SSR setup exists — in that case Vue's SSR APIs (`createSSRApp`, `onServerPrefetch`, `createServerRenderer`) apply. Async: `defineAsyncComponent`, `<Suspense>`, `<Teleport>`. The skill applies SSR-specific rules ONLY when SSR is detected.

**Version verification (MANDATORY):** see `1-audit.md`.

## Preconditions

1. Repository read access; record commit hash.
2. Reports at `reports/YYYY-MM-DD/vue-03-rendering-review.md`; shared log initialized.
3. Determine rendering mode: is there an SSR layer (`ssr`/`createSSRApp`/Nuxt/VitePress/`renderToString`)? If not, skip all SSR sections and mark the report "SPA-only".

## Audit Objectives

1. Classify the app's rendering mode and verify it matches the deployment and requirements.
2. Detect hydration mismatches and browser-only API misuse (only if SSR exists).
3. Detect async-rendering defects (Suspense, async components, Teleport).
4. Detect rendering-performance issues (lazy rendering, unnecessary re-renders — delegate depth to `3-performance.md`).

## Audit Rules

### Mode classification
1. **Mode verification:** determine SPA vs SSR/SSG from the entry (`main.ts` `createApp` vs `createSSRApp`/SSR entry), build config, and deployment. Record the verdict. Flag mismatches: SSR code present but deployed as static SPA; SPA expected but SSR artifacts served — HIGH (broken app).
2. **SSR presence:** if no SSR layer exists, SSR rules below do not apply — mark and move on. Do not invent SSR findings for pure SPAs (false positive).

### SSR & hydration (conditional)
3. **Hydration mismatches:** if SSR exists, flag code producing divergent server/client output: `Date.now()`/`new Date()` at render, `Math.random()`, `window`/`document` access during setup/render, browser-only storage reads — MEDIUM/HIGH (broken interactivity, flash).
4. **Browser-only APIs unguarded:** flag `window`/`document`/`localStorage`/`sessionStorage` used during SSR render without guard (`onMounted`/`import.meta.client`/`process.client`-style checks) — HIGH if it breaks SSR, MEDIUM otherwise.
5. **`onServerPrefetch`:** if SSR + Vue Router data fetching exist, flag missing `onServerPrefetch` for data that must be server-rendered (indexability/SEO) — MEDIUM/HIGH.
6. **SSR data exposure:** if SSR exists, flag private/authenticated data serialized into SSR HTML/payload without need — HIGH (see Nuxt `5-ssr.md` for the equivalent pattern).

### Async & rendering structure
7. **Async components:** flag `defineAsyncComponent`/dynamic imports used where eager import is warranted (small, critical-path components) — LOW; the inverse (heavy components eager) is `3-performance.md`'s finding.
8. **Suspense misuse:** flag missing `<Suspense>` fallback for async components causing layout jumps/blanks; flag nesting Suspense where a single boundary suffices — LOW/MEDIUM.
9. **Teleport misuse:** flag `<Teleport to="body">` for content that should stay in component context (a11y/stacking issues), missing `:disabled` handling in SSR contexts — LOW.
10. **Dynamic components:** flag `component :is` bound to user input (see `2-security.md`) and unbounded dynamic component trees hurting render performance — MEDIUM.
11. **Lazy rendering:** flag above-fold content rendered lazily (UX regression) and below-fold heavy content rendered eagerly (perf) — LOW/MEDIUM.

## Detection Logic

1. Read `main.ts`/entry: `createApp` vs `createSSRApp`; check for SSR entry files, `vite.config` SSR settings, or a meta-framework dependency.
2. If SSR: grep `window`, `document`, `localStorage`, `sessionStorage`, `Date`, `Math.random` in setup/render code paths.
3. Inventory async components, Suspense boundaries, Teleport usage.
4. Capture rendered output if runnable (SPA: mount output; SSR: server-rendered HTML) to verify behavior.

## Evidence Requirements

- Rendering-mode verdict with entry/build evidence.
- Hydration mismatch: the divergent code + observed mismatch where captured.
- SSR exposure: the serialized payload/HTML excerpt.
- Async structure: file/line for Suspense/Teleport/async-component findings.

## Severity

- HIGH: SSR breaks at runtime (unguarded browser APIs), SSR data exposure on authenticated data, mode/deployment mismatch.
- MEDIUM: hydration mismatches, missing `onServerPrefetch` for indexable content, unbounded dynamic component trees.
- LOW/INFO: Suspense/Teleport hygiene, lazy-rendering tweaks.

## False Positives

- SPA apps legitimately use `localStorage`/`window` freely — no SSR rules apply.
- `Date.now()` in a click handler is fine — only render-time divergence matters.
- Teleport to body is a legitimate pattern (modals) — flag only misuse.
- Absence of `onServerPrefetch` in an SPA is meaningless.

## Remediation

- Guard browser-only code (`onMounted`, client-only branches); use deterministic render values.
- Add `onServerPrefetch` where server data is required; keep private data out of SSR output.
- Right-size Suspense boundaries; lazy-load only below-fold/heavy content.

## Validation

Re-render the affected views (SSR + client) and confirm no mismatch; verify data exposure is gone; re-run tests.

## Cross-Layer Considerations

- SSR data fetching hits backend APIs — coordinate with `6-api.md` and backend API skills.
- Rendering mode determines SEO and caching behavior — coordinate with `7-infrastructure.md` and backend caching rules.

## References

- https://vuejs.org/guide/scaling-up/ssr, https://vuejs.org/guide/built-ins/suspense, https://vuejs.org/guide/built-ins/teleport
- https://vitejs.dev/guide/ssr

## Report & Log Integration

Progressive persistence to `reports/YYYY-MM-DD/vue-03-rendering-review.md`; shared log block; shared finding schema.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** Editorial context; the executing model MUST ignore this section.

The explicit "do not assume SSR" stance is the skill's defining rule — most Vue apps in the wild are SPAs, and a generic audit that applies SSR rules to them produces noise. The conditional structure (mode verification first, SSR rules gated) is the mechanism that keeps the skill honest; it mirrors the Nuxt `5-ssr.md` doctrine from the other side of the same coin.
