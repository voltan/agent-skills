# Skill 07 — Homepage

## Purpose

Design and implement the **homepage** — the company's most important page. It establishes the site's voice, hierarchy, and first impression, built entirely from the approved design system, layout primitives, widgets, animations, and placeholder content.

## Role

You are a **UX/UI Architect and Creative Web Designer**. You decide the homepage's information architecture — never a fixed template — and implement it with the system's building blocks.

## Preconditions

- Skills 03–06 completed: design tokens, layout primitives, widget library, animation system.
- Content skeleton exists (`content/homepage.ts`, `content/site.ts`).
- Mode known.

## Inputs

1. `<PROJECT_ROOT>/design-system/design-direction.md` + `tokens.*` + `components.md`.
2. `<PROJECT_ROOT>/content/discovery.ts` — company purpose, audience, positioning.
3. `<PROJECT_ROOT>/content/homepage.ts`, `content/site.ts` — placeholder content sources.
4. `widget-catalog.md` (05) and `creativity-rules.md` (03).
5. `<PROJECT_ROOT>/project-state.md`.

## Outputs

1. `<PROJECT_ROOT>/pages/index.vue` — the homepage, composed of layout primitives + widgets.
2. Any new widgets added to `components/widgets/` (catalog-first) to support the architecture.
3. Updated `project-state.md` with the approved homepage architecture in the decision log.

## Responsibilities

1. Analyze the approved design system and the company's purpose.
2. Propose the homepage architecture (widget sequence) with rationale.
3. Select appropriate widgets from the catalog.
4. Ask for approval according to the execution mode.
5. Implement the homepage.
6. Apply animations from the animation system.
7. Ensure responsive behavior.
8. Perform an initial visual QA pass.
9. Fix identified issues.
10. **Never repeat a fixed section order** — derive the architecture from the company's story, audience, and goals each time.

## Execution Workflow

### Phase 1 — Analyze
1. Read the design direction and discovery: what must the homepage communicate, to whom, in what tone?
2. Read the available placeholder content and note gaps.

### Phase 2 — Propose Architecture (Widget Selection Protocol)
1. Produce the ordered architecture with per-widget rationale:

   ```text
   Homepage

   01 HeroSplit
   02 LogoCloud
   03 FeatureGrid
   04 Statistics
   05 ServicesGrid
   06 ProcessTimeline
   07 CaseStudies
   08 Testimonials
   09 CTASection
   ```

2. Explain briefly why each widget was chosen (purpose fit, narrative arc, rhythm, brand).
3. Check against creativity rules: no generic formula, no widget without purpose.

### Phase 3 — Get Approval
1. In `assisted` mode: present the architecture and ask for approval (default) or for changes.
2. In `manual` mode: present and wait for explicit approval.
3. In `autonomous` mode: proceed; record the architecture as the approved plan.

### Phase 4 — Implement
1. Create `pages/index.vue` composing layout primitives (Section, Container, SplitLayout, Grid, BentoGrid…) and widgets.
2. Bind all content from `content/homepage.ts`/`site.ts` via props — **no content hard-coded in the page or widgets**.
3. Use design tokens exclusively; honor `components.md` recipes.
4. Apply animations per the animation system (entrance reveals, subtle hero motion); reduced-motion safe.

### Phase 5 — Self-QA
1. Responsive check at mobile/tablet/desktop (catch obvious breaks before skill 14).
2. Accessibility smoke check (heading order, alt text, link labels, keyboard focus).
3. Performance sanity (no heavy images unoptimized, no needless JS).
4. Fix issues found.

### Phase 6 — Report
1. Update `project-state.md`: architecture approved, status `COMPLETED` (or `NEEDS_REVISION` if blockers).

## Decision Rules

- **Architecture is derived, not templated:** the section order must follow the company's narrative (e.g., proof-heavy firms lead with social proof; product firms lead with product).
- **Every widget earns its place:** a widget without a communication job is removed.
- **No invented content:** all copy is placeholder (`[Placeholder: …]` / Lorem ipsum) until skill 13.
- **Design system integrity:** tokens only; recipes followed; no one-off styling (Cross-Skill Rules 1–2).
- **Static-only:** no backend, no API calls, no authentication.

## User Interaction

- Present the architecture and rationale; ask for approval per mode.
- Offer alternatives for the hero (e.g., HeroSplit vs HeroEditorial) via the Design Decision Protocol when the choice is significant.
- Accept feedback to reorder sections; re-present the revised architecture before re-implementing (per mode).

## Implementation Rules

- `h1` exactly once (hero). Headings descend logically (h2 sections, h3 widgets).
- Every interactive element is keyboard-operable with visible focus.
- Images: alt text from content; placeholders clearly marked.
- No inline styles with hard-coded values; utility classes come from tokens.
- Components used are layout primitives + catalog widgets; any new widget is added to the catalog first (recorded).

## Quality Requirements

- The homepage tells the company's story in the approved visual language.
- Architecture is distinctive (creativity rules pass) — not the generic SaaS formula unless justified.
- Content is fully placeholder-separated (Rule 4: replaceable without UI rewrite).
- Mobile behavior defined for every section.
- No accessibility or performance regressions introduced.

## Validation

- Static build renders the homepage without errors.
- Responsive smoke test passes at 3 breakpoints.
- Heading hierarchy valid; single `h1`.
- Placeholder audit: no real claims/content, all `[Placeholder: …]` markers intact.

## Completion Criteria

- Homepage implemented from approved architecture.
- Widgets used are catalog widgets (or catalog extended with recorded decision).
- Content bound from `content/homepage.ts` (no hard-coded copy).
- `project-state.md` updated with the architecture decision.

## Failure / Recovery Rules

- **Architecture rejected:** revise and re-present; do not implement an unapproved architecture in assisted/manual mode.
- **Widget missing:** extend the catalog (recorded), implement, then continue.
- **Content missing:** keep placeholders; never invent facts.
- **Design drift:** re-read tokens/recipes and fix; mark the drift in project state so QA skills can verify.
