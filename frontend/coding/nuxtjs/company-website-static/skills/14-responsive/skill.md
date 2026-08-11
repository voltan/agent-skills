# Skill 14 — Responsive

## Purpose

Validate and **fix** the website across all target viewports — Mobile, Tablet, Laptop, Desktop, Large Desktop. Responsive problems are corrected in the implementation, not merely reported.

## Role

You are a **Responsive Design Engineer**. You find breakages at every width and repair them at the right layer (tokens → layout primitives → widgets → page composition).

## Preconditions

- Pages exist (07–12) and content is in place (13) — or at least the pages render.
- Build runs; mode known.

## Inputs

1. Design system tokens (breakpoints, container widths, spacing).
2. Layout primitives and widget catalog (the layers where fixes belong).
3. The built website (dev server or static output).
4. `project-state.md`.

## Outputs

1. Fixed implementations (tokens/primitives/widgets/pages as needed).
2. `<PROJECT_ROOT>/docs/responsive-report.md` — tested viewports, issues found, fixes applied, residual issues (with reasons).
3. Updated `project-state.md`.

## Target Viewports (validate all)

```text
Mobile        ~360–414 px
Tablet        ~768 px
Laptop        ~1024–1280 px
Desktop       ~1280–1440 px
Large Desktop  ~1512+ px
```

## Check List

- **Navigation:** header collapses correctly; mobile menu opens/closes; no overlapping links.
- **Typography:** no overflow, no orphaned words, fluid type scales, line lengths acceptable.
- **Grid:** column counts collapse as designed; no empty or broken columns; order correct.
- **Spacing:** rhythm holds; no crushed or excessive gaps.
- **Images:** correct `object-fit` crops; aspect ratios preserved; no distortion.
- **Cards:** height consistency within rows; content truncates gracefully.
- **Buttons:** full-width on mobile where designed; touch targets ≥ 44×44px.
- **Forms:** fields and labels stack; inputs usable on touch.
- **Tables:** horizontal scroll (with accessible pattern) or card conversion on mobile.
- **Overflow:** zero horizontal page overflow at any width.
- **Touch targets:** all interactive elements meet minimum size and spacing.
- **Section order:** mobile stacking order makes sense (content-first where it matters).
- **Hero composition:** hero media + copy remain legible; no cut-off copy.
- **Animation behavior:** reveals trigger at mobile; parallax/marquee simplified or disabled; no jank.

## Responsibilities

1. Validate every route at all five target viewports.
2. Check navigation, typography, grids, spacing, images, cards, buttons, forms, tables, overflow, touch targets, section order, hero composition, and animation behavior.
3. Fix responsive problems at the correct layer (tokens → primitives → widgets → pages), not just report them.
4. Verify fixes across all viewports and confirm the static build still passes.

## Execution Workflow

### Phase 1 — Establish the Baseline
1. Run the dev server / static preview.
2. Enumerate all routes (pages, detail pages, legal) to test.

### Phase 2 — Sweep Every Viewport
1. For each viewport (Mobile → Large Desktop) and each route: walk the check list.
2. Record findings with: route, viewport, element, symptom, and suspected layer (token/primitive/widget/page).

### Phase 3 — Fix at the Right Layer
1. **Token layer:** wrong breakpoints/container widths → fix tokens once (affects everywhere).
2. **Primitive layer:** a layout primitive misbehaves → fix the primitive, not each page.
3. **Widget layer:** a widget breaks at a width → fix the widget's responsive definition (its catalog spec requires mobile behavior).
4. **Page layer:** composition issue unique to a page → fix the page composition.
5. Re-test after every fix class.

### Phase 4 — Verify
1. Re-run the sweep on all viewports for the fixed routes.
2. Confirm zero horizontal overflow and touch-target compliance.
3. Run the static build to confirm nothing broke.

### Phase 5 — Report
1. Write `docs/responsive-report.md`.
2. Update `project-state.md`.

## Decision Rules

- **Fix the cause, not the symptom:** a repeated breakage is a primitive/token bug; page-level hacks are not acceptable fixes.
- **Mobile-first:** resolve mobile first, then confirm larger breakpoints.
- **No layout-API animations:** responsive fixes never introduce width/height animations.
- **Design-system integrity:** fixes use tokens; no one-off magic values (Cross-Skill Rule 1).
- **Honesty:** residual issues are documented with reasons (e.g., "video hero replaced by poster on mobile by design").

## User Interaction

- Report the issue/fix summary per viewport.
- In `assisted`/`manual` mode, get approval before changes that alter the approved responsive behavior (e.g., changing breakpoints, reordering sections on mobile).
- In `autonomous` mode, fix and log.

## Implementation Rules

- Every widget already defines mobile behavior (Rule 5); if one doesn't, add it to the widget (and its catalog spec).
- Touch targets: minimum 44×44 px (WCAG 2.2 target size AA where feasible), adequate spacing between targets.
- No fixed heights on text containers (overflow risk); use min/max + clamp where needed.
- Tables: real `table` semantics preserved with a scroll container or card pattern — never break semantics for responsiveness.

## Quality Requirements

- Zero horizontal overflow at all target viewports.
- All interactive elements reachable and touch-friendly on mobile.
- Typography readable and rhythm consistent at every width.
- All five viewports pass the check list.

## Validation

- Full sweep passes at 5 viewports.
- Static build passes.
- Residual issues documented with reasons.

## Completion Criteria

- All identified responsive issues fixed (or documented as intentional design decisions).
- Report written; `project-state.md` updated.

## Failure / Recovery Rules

- **Recurring breakage:** suspect the primitive/token; fix centrally; verify all pages.
- **Conflicting fixes:** if a fix breaks another viewport, re-test all; prefer token/primitive-level solutions.
- **Unfixable within budget:** document the issue and route it to the user for a design decision (or to skill 18 visual QA for a visual judgment).
