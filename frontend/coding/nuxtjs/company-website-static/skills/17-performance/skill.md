# Skill 17 — Performance

## Purpose

Audit and **optimize** frontend performance so the static site loads fast, animates smoothly, and ships minimal unnecessary code. Performance is built in by earlier rules (Rule 7); this skill verifies and fixes.

## Role

You are a **Performance Engineer**. You measure the real output, find waste, and remove it — favoring static HTML, minimal JavaScript, and CSS-first solutions.

## Preconditions

- Pages exist and are complete (07–13); the site builds.
- Mode known.

## Inputs

1. The static build output (`.output`/`dist`).
2. Asset inventory (images, fonts, videos).
3. The animation system (to verify motion performance).
4. `project-config/project-config.md` (+ `brand/`, `references/`) — brand assets copied into the site must be optimized like any other asset.
5. `project-state.md`.

## Outputs

1. Optimized implementation (code, assets, config).
2. `<PROJECT_ROOT>/docs/performance-report.md` — measurements, budgets, fixes, residual items.
3. Updated `project-state.md`.

## Check List

- **JavaScript size:** total JS payload small; route-level splitting; no unused dependencies; interactive widgets import only when used.
- **CSS size:** stylesheet lean; Tailwind purged; no unused classes; no duplicated styles.
- **Image optimization:** all images optimized (WebP/AVIF where supported), compressed, correct dimensions; no oversized originals.
- **Lazy loading:** below-fold images/videos lazy-loaded (`loading="lazy"` / Nuxt Image idioms); hero images eager with width/height set.
- **Font loading:** font-display swap; subsetting; preload critical fonts; no render-blocking font piles; self-hosted or approved CDN.
- **Animation performance:** transform/opacity only; no layout-triggering animations; GPU-composited; reduced-motion already covered (skill 06).
- **Layout shifts:** all media have reserved dimensions (width/height or aspect-ratio); zero CLS on load and scroll.
- **Unnecessary dependencies:** audit `package.json`; remove unused packages; no heavyweight libs for one-off needs.
- **Component complexity:** no bloated components; render scope minimal; `v-if` vs `v-show` correct; no wasted reactivity.
- **Client-side JavaScript:** content pages static-first; minimal hydration work; no client-only content for SEO-critical text.
- **Hydration:** hydration cost measured; components outside the interactive area stay inert where possible (islands approach if the installed Nuxt version supports it reasonably).
- **Third-party resources:** no analytics/tracking/chat scripts unless explicitly approved; any allowed scripts loaded async/deferred and documented.
- **Network requests:** few requests; assets cached; no render-blocking requests in the critical path; preconnect only where used.

## Preferred Approaches

```text
Static HTML
Minimal JavaScript
Optimized images
Lazy loading
CSS-first solutions
Progressive enhancement
```

Avoid unnecessary client-side complexity.

## Responsibilities

1. Measure the real static output (JS/CSS sizes, image weights, network, LCP/CLS).
2. Audit and fix: JavaScript size, CSS size, image optimization, lazy loading, font loading, animation performance, layout shifts, unnecessary dependencies, component complexity, client-side JavaScript, hydration, third-party resources, and network requests.
3. Prefer static HTML, minimal JavaScript, optimized images, lazy loading, CSS-first solutions, and progressive enhancement.
4. Verify no regressions in function, accessibility, or SEO after optimization.

## Execution Workflow

### Phase 1 — Measure
1. Build the site; inspect output sizes (JS/CSS/HTML) and asset weights.
2. Run a lighthouse-style measurement (or compute the core metrics manually): LCP, CLS, TBT, total bytes, request count.
3. Record the baseline.

### Phase 2 — Audit Code and Assets
1. Walk the check list; identify the top waste items (largest JS chunk, heaviest images, unused deps).
2. Verify animation and hydration behavior.

### Phase 3 — Optimize (biggest wins first)
1. **Images:** optimize/serve correct sizes/formats; set dimensions.
2. **JS:** remove unused deps; code-split widgets; defer third-party.
3. **CSS:** purge/trim; eliminate duplication.
4. **Fonts:** subset + swap + preload.
5. **HTML:** ensure static-first rendering; reserve media space (CLS).
6. Re-measure after each major change.

### Phase 4 — Verify
1. Re-run measurement; confirm improvements; ensure no regressions in function/accessibility/SEO.
2. Static build passes.

### Phase 5 — Report
1. Write `docs/performance-report.md` with baseline vs final metrics and budgets.
2. Update `project-state.md`.

## Decision Rules

- **Static-first:** prefer shipping HTML/CSS over JS for every feature that can be static.
- **Measure, don't guess:** every optimization is justified by a measurement.
- **No regression trade-offs:** performance changes never break accessibility, SEO, or the approved design.
- **No speculative deps:** no dependency added without a measured need; any new dependency is approved and documented.
- **Effects pay rent:** any animation/effect must pass the purpose test (creativity rules) and the performance budget.

## User Interaction

- Report baseline vs final metrics.
- In `assisted`/`manual` mode, get approval before removing functionality (e.g., dropping a third-party script) or adding a dependency.
- In `autonomous` mode, optimize and log decisions.

## Implementation Rules

- Images: Nuxt Image idioms (or equivalent) with explicit `width`/`height`/`alt`; formats and breakpoints per design tokens.
- Fonts: self-hosted subsets with `font-display: swap`; preload the primary font.
- Code splitting: page-level and widget-level lazy imports where measurable win.
- No inline critical CSS hacks unless a measurement shows they matter and the complexity is documented.
- All measurements recorded with the tool/method used.

## Quality Requirements

- Bundle budget met (documented thresholds, e.g., < 100KB gzip JS baseline for a content site — verify against actual content).
- Zero avoidable CLS; LCP under the agreed budget; no layout-triggering animations.
- All images optimized and dimensioned.
- No unused dependencies in `package.json`.
- Third-party count near zero (only approved scripts).

## Validation

- Re-measured metrics show improvement vs baseline.
- Static build passes; pages function; accessibility/SEO checks from skills 15/16 still pass.

## Completion Criteria

- Optimization applied and measured.
- Performance report written with budgets; residual items documented.
- `project-state.md` updated.

## Failure / Recovery Rules

- **Budget miss:** find the largest remaining cost center; iterate; if the budget is unrealistic for the content, document and agree a revised budget with the user.
- **Optimization breaks a feature:** revert the specific change; choose an alternative approach; never ship a broken feature for bytes.
- **Hydration cost:** reduce interactive surface or use islands/partial hydration per installed Nuxt capabilities; document.
