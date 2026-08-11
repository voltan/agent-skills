# Vue Performance Audit Skill

> **Frontend Audit Suite — Enhanced for DeepSeek-V4 Flash.** Stepwise, deterministic, schema-driven.

## Purpose

Detect performance defects specific to Vue's reactivity and rendering model: unnecessary reactivity, expensive watchers/computeds, wasteful re-renders, large lists, bundle size, and main-thread blocking — with evidence.

## Scope

Unnecessary reactivity, deep reactivity, large reactive objects, watchers, expensive computed values, unnecessary renders, unstable keys, large lists, virtualization, lazy components, async components, code splitting, dynamic imports, bundle size, third-party dependencies, images, fonts, third-party scripts, long tasks, main-thread blocking, DOM size.

## Framework Context

Vue 3.5: reactivity via `ref`/`reactive`/`computed`/`watch`/`watchEffect`, `shallowRef`/`shallowReactive`, `markRaw`, `toRaw`, `defineAsyncComponent`/`<Suspense>`, `v-memo`, `v-once`, `v-for` key requirements, `<Teleport>`, `onMounted` etc. Vite code splitting via dynamic imports. Main-thread work is the dominant cost in SPA interactions (INP).

**Version verification (MANDATORY):** see `1-audit.md`.

## Preconditions

1. Repository read access; record commit hash.
2. Reports at `reports/YYYY-MM-DD/vue-05-performance-review.md`; shared log initialized.
3. Build output or a runnable app preferred for measurement.

## Audit Objectives

1. Identify reactivity misuse that causes avoidable re-renders and wasted work.
2. Identify expensive watchers/computeds/lists and main-thread blockers.
3. Identify bundle/code-splitting issues and third-party bloat.
4. Identify asset (image/font/script) delivery issues.

## Audit Rules

### Reactivity
1. **Deep reactivity on large structures:** flag `reactive()`/`ref()` wrapping huge objects (tables, documents, config) where only top-level reads/writes occur — use `shallowRef`/`markRaw`/`toRaw`. Deep proxies add overhead and can blow up memory. HIGH for very large structures.
2. **Unnecessary reactivity:** flag `ref`/`reactive` on values never read reactively (constants, static config, DOM-only data) — LOW/MEDIUM; flag `reactive` on data that should be plain objects.
3. **Expensive computed values:** flag computeds with heavy work (filtering/sorting large arrays, string processing) recomputed on every dependency change without memoization or `cache` awareness — MEDIUM; flag computeds with side effects (anti-pattern, causes subtle re-render bugs) — MEDIUM.
4. **Watchers:** flag `watch`/`watchEffect` doing heavy work on every change, deep `watch` (`{ deep: true }`) on large structures, missing `immediate`/`flush` considerations causing extra runs — MEDIUM; flag `watchEffect` touching many reactive deps (broad invalidation).
5. **Unnecessary renders:** flag components re-rendering due to: parent re-render passing unstable props (new object/array literals in template bindings), missing `v-memo`/`v-once` on stable subtrees, reactive state read in the wrong component scope — MEDIUM. Flag excessive global state (store) reads causing wide re-renders.
6. **Unstable keys:** flag `v-for` using index as key when list order/content changes (incorrect reuse, state bugs + wasted renders) — MEDIUM; flag missing keys.

### Lists & rendering
7. **Large lists without virtualization:** flag rendering hundreds/thousands of rows without virtualization (e.g., `vue-virtual-scroller`/`@tanstack/vue-virtual`) — MEDIUM/HIGH for large datasets; DOM size is an INP factor.
8. **Lazy/async components:** flag heavy components (editors, charts, maps, admin-only) imported eagerly — MEDIUM; use `defineAsyncComponent`/dynamic import; flag async components without Suspense fallback handling causing jank.
9. **Lazy rendering:** flag below-fold heavy content rendered immediately instead of lazy (`v-if` on visibility) — LOW/MEDIUM.

### Bundle & assets
10. **Bundle size & code splitting:** flag route-level chunks not used (SPA with single big chunk), heavy libs in the initial chunk, `import` of server-only/unused libs client-side — MEDIUM; verify with `vite build` output analysis.
11. **Third-party dependencies:** flag large utility libs replacing small native implementations (bundle cost), duplicate packages — LOW/MEDIUM (see `8-dependencies.md`).
12. **Images:** flag unoptimized images (no `width`/`height` → CLS, no lazy loading below fold, no srcset/AVIF/WebP, oversized originals) — MEDIUM; hero/LCP image without preload — MEDIUM.
13. **Fonts:** flag render-blocking fonts without `font-display: swap`, multiple weights without subsetting, no preload — MEDIUM.
14. **Third-party scripts:** flag render-blocking analytics/widget scripts without `defer`/`async`, missing SRI on external scripts — MEDIUM (SRI also a security note).

### Main thread
15. **Long tasks:** flag heavy synchronous work in click/scroll handlers or `onMounted` (large parsing, sorting, layout reads in loops) blocking the main thread (INP) — MEDIUM/HIGH with evidence; recommend chunking/workers/`requestIdleCallback`.
16. **Layout thrash:** flag alternating read/write DOM access in loops — MEDIUM.
17. **Memory:** flag unbounded caches/arrays in stores, missing cleanup of timers/listeners/observers on unmount — MEDIUM (leak).

## Detection Logic

1. Grep reactivity APIs (`ref(`, `reactive(`, `computed(`, `watch(`, `watchEffect(`, `shallowRef`, `markRaw`, `toRaw`) and `v-for`/keys, `defineAsyncComponent`.
2. Profile large-data usage: tables, lists, filters, documents.
3. Run build analysis (`vite build` + `rollup-plugin-visualizer`/`vite-bundle-visualizer`) for chunk sizes.
4. Inspect image/font/script usage for optimization gaps.
5. Where possible, measure with a performance trace (Lighthouse INP/LCP, `performance.mark`).

## Evidence Requirements

- File/line for each misuse with the reactive value/large structure referenced.
- Build analysis output (chunk sizes) for bundle findings.
- Measured long-task/INP evidence where available; otherwise the offending synchronous code path.
- List sizes and rendering approach for virtualization findings.

## Severity

- HIGH: large deep-reactive structures causing measurable jank; unbounded lists without virtualization on core views; heavy synchronous work in interaction handlers.
- MEDIUM: expensive watchers/computeds, unnecessary renders, unstable keys, missing code splitting for heavy routes, unoptimized images/fonts.
- LOW/INFO: minor optimization opportunities, micro-optimization notes.

## False Positives

- `v-for` with index keys is fine for static, order-stable lists.
- Not every `ref` needs `shallowRef` — only flag large structures with real cost.
- A store read per component is normal; only wide invalidation with heavy recomputation is a finding.
- SSR-less SPAs have no SSR hydration costs — apply hydration rules only where SSR exists (`5-rendering.md`).

## Remediation

- Use `shallowRef`/`markRaw` for large inert structures; memoize expensive computeds; narrow watcher deps.
- Virtualize large lists; lazy-load heavy components; add `v-memo`/`v-once` judiciously.
- Code-split routes/heavy libs; optimize images (dimensions, lazy, responsive formats) and fonts (swap, subset).
- Chunk long tasks; clean up subscriptions on unmount.

## Validation

Re-measure after fixes (build sizes, trace, Lighthouse); verify re-render counts and INP improved; ensure no regression in correctness.

## Cross-Layer Considerations

- API payload size and missing backend pagination drive frontend list/render cost — `Cross-Layer` with `backend/audit/nestjs/2-performance-audit.md` / `backend/audit/mezzio/mezzio-architecture-performance-audit.md`.
- CDN caching of static assets (hashed chunks) is a backend/infra concern — coordinate with `7-infrastructure.md`.

## References

- https://vuejs.org/guide/best-practices/performance (official performance guide)
- https://web.dev/vitals (INP/LCP), https://vitejs.dev/guide/features (dynamic import)

## Report & Log Integration

Progressive persistence to `reports/YYYY-MM-DD/vue-05-performance-review.md`; shared log block; shared finding schema.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** Editorial context; the executing model MUST ignore this section.

Vue performance audits over-report on reactivity API presence; this skill anchors every rule to a cost (re-render, memory, long task) and to evidence, with explicit false-positive rules for index keys and plain refs. The list-virtualization and long-task rules target the two dominant real-world SPA pain points (large datasets, interaction latency), and the deep-reactivity rule captures a genuinely common Vue-3-specific mistake.
