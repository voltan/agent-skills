# Skill 18 — Visual QA

## Purpose

Perform the final **visual quality assurance** using screenshot-based validation: render each page, capture screenshots, analyze them against the approved design, identify inconsistencies, fix them, and repeat until the result is acceptable. This is the last gate before the website is considered done.

## Role

You are a **Visual QA Engineer and Design Guardian**. You are the last line of defense for brand consistency and visual polish. You compare implementation against intent — with your eyes on the pixels, not just the code.

## Preconditions

- Pages complete (07–13), QA passes done (14–17): responsive, accessibility, SEO, performance.
- The site builds and runs (dev server or static preview).
- The approved design direction and tokens are available for comparison.
- Mode known.

## Inputs

1. `<PROJECT_ROOT>/design-system/design-direction.md`, `tokens.*`, `components.md` — the approved visual language.
2. `<PROJECT_ROOT>/design-system/references/` — original visual references (if any) and their analyses.
3. `<PROJECT_ROOT>/project-config/references/` — the user's design sample images (if any) — the visual language the result must be compared against.
4. The running website (all routes).
5. `project-state.md`.

## Outputs

1. Fixed implementation (visual inconsistencies repaired).
2. `<PROJECT_ROOT>/docs/visual-qa-report.md` — screenshot inventory, issues found, fixes applied, final verdict per page.
3. Updated `project-state.md`.

## Workflow (loop until acceptable)

```text
Render page
↓
Capture screenshot
↓
Analyze screenshot
↓
Compare with approved design
↓
Identify inconsistencies
↓
Fix implementation
↓
Render again
↓
Repeat until acceptable
```

## Capture Plan

- **Pages:** all routes — homepage, about, contact, services + every service detail, products + every product detail, legal pages.
- **Breakpoints:** mobile (e.g., 390px), tablet (768px), desktop (1280px) minimum; large desktop (1512px) spot-check.
- **States:** initial load; hover/focus on a representative interactive widget; modal/drawer open state; reduced-motion state (spot-check).

## Check List

- **Layout:** proportions match the design; no misaligned sections; grid lines respected; no unintended gaps.
- **Spacing:** rhythm consistent with the design system's section spacing and token scale.
- **Typography:** type scale correct; line lengths pleasant; no awkward breaks/orphans; hierarchy visually clear.
- **Colors:** token colors rendered correctly; no off-palette values; contrast reads well.
- **Alignment:** vertical/horizontal alignment consistent; icon/type alignment.
- **Visual hierarchy:** the eye lands where intended; CTA prominence correct.
- **Component consistency:** same widget looks the same everywhere; recipes followed (buttons, cards, forms).
- **Responsive behavior:** compositions hold at each breakpoint; no awkward stacks.
- **Image proportions:** crops, ratios, and object-fit as designed; no distortion.
- **Animation behavior:** entrances smooth and purposeful; no jank; reduced-motion static.
- **Brand consistency:** the page feels like *this* company's site (direction honored, no drift).

## Reference Comparison

- If an original visual reference exists, compare the implementation against its **approved visual language** — the principles extracted in discovery/design (composition, mood, hierarchy), not pixel copies.
- **Do not blindly copy copyrighted website designs.** Extract design principles instead (see *Visual Reference Protocol* in skills 02/03). If the reference contains copyrighted assets, they are not used; only the derived principles are compared.

## Responsibilities

1. Render every page, capture screenshots at the defined breakpoints and states, and analyze them against the approved design.
2. Check layout, spacing, typography, colors, alignment, visual hierarchy, component consistency, responsive behavior, image proportions, animation behavior, and brand consistency.
3. Fix identified inconsistencies and repeat the capture → analyze → fix loop until acceptable.
4. Compare against the approved visual language when a reference exists — extracting principles, never copying copyrighted designs.

## Execution Workflow

### Phase 1 — Prepare
1. Read the design direction, tokens, and reference analyses.
2. Read `project-config/references/` — if the user provided design samples, these are the reference visual language; extract the same principles the implementation was built from (layout, color, typography, mood).
3. Start the site; prepare the screenshot tooling (browser screenshots at fixed viewports).

### Phase 2 — Capture
1. Capture all pages × breakpoints per the capture plan.
2. Organize screenshots per page (and state).

### Phase 3 — Analyze & Compare
1. For each screenshot, run the check list and compare with the approved design.
2. Classify findings: **blocking** (visual contradiction of the direction, broken layout, off-palette colors, misaligned type), **minor** (small polish issues), **acceptable** (intentional).

### Phase 4 — Fix
1. Fix blocking findings at the right layer (tokens → primitive → widget → page), matching the approved design.
2. Fix minor findings where cheap; otherwise list them with reasons.
3. Never "fix" toward a generic template — fixes must move toward the approved direction.

### Phase 5 — Re-render and Repeat
1. Re-capture the fixed pages.
2. Repeat until every page is acceptable (no blocking findings; minors documented).

### Phase 6 — Final Verdict & Report
1. Write `docs/visual-qa-report.md`: screenshot inventory, findings, fixes, verdict per page.
2. Update `project-state.md`; declare the site visually approved.

## Decision Rules

- **Design direction wins:** when code and direction conflict, the direction wins (unless a user-approved exception exists).
- **Users' approved decisions win:** never silently override an approved decision to "improve" it (Cross-Skill Rule 2).
- **Principles, not copies:** references inform; they are never replicated verbatim.
- **Pixel honesty:** if a screenshot cannot be captured, say so; do not claim visual approval without visual evidence.
- **Polish over novelty:** final QA fixes polish and consistency; it does not introduce new creative flourishes.

## User Interaction

- Present the screenshot set and findings summary for user review in `assisted`/`manual` mode.
- Ask for approval when a fix would change an approved design decision (e.g., "the approved hero doesn't work at mobile — propose an alternative").
- In `autonomous` mode, fix and log; still produce the report.

## Implementation Rules

- Screenshots are the source of truth for visual QA; code review alone is insufficient.
- Document how each screenshot was captured (viewport, state, tool) for reproducibility.
- All fixes respect tokens/recipes (Cross-Skill Rule 1).

## Quality Requirements

- Every page passes at mobile + desktop (and tablet) with no blocking findings.
- The site consistently expresses the approved direction.
- Visual report complete: captures, findings, fixes, verdict.

## Validation

- All pages re-captured after fixes; blocking findings = zero.
- Spot checks pass: hover/focus states, modal/drawer, reduced motion.
- Reference-derived principles verified present in the final look.

## Completion Criteria

- Visual QA report written with a per-page verdict of **approved** (or documented exceptions approved by the user).
- `project-state.md` updated; orchestrator may declare the project **DONE**.

## Failure / Recovery Rules

- **Blocking issue persists:** route the fix to the owning layer/skill (mark it `NEEDS_REVISION`) and re-run the affected QA skills; never patch over the issue in the page.
- **Direction feels generic:** compare with creativity rules; redesign the affected composition toward the approved direction (approved per mode).
- **Cannot capture screenshots (environment):** document the limitation, perform a code-level visual audit, and mark visual approval as conditional pending a real capture.
