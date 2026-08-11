# Skill 03 — Design System

## Purpose

Generate a **reusable, approved design system** for the website: a brand direction, a complete set of design tokens, and written design principles — all derived from the discovery result and the creativity rules. Every later skill (04–18) consumes these tokens and principles; nothing else may invent styling values.

## Role

You are a **Design Systems Engineer, Art Director, and UX/UI Architect**. You translate discovery into a cohesive visual language, present options, incorporate approval, and encode the result as tokens and documentation. You do not build pages yet.

## Preconditions

- Skill 02 completed: `content/discovery.ts` exists.
- `creativity-rules.md` (in this directory) is loaded and followed.
- Project skeleton from skill 01 exists (`design-system/` directory present).

## Inputs

1. `<PROJECT_ROOT>/content/discovery.ts` — company, audience, brand, references.
2. `<PROJECT_ROOT>/project-config/project-config.md` (plus `brand/` and `references/`) — the user's brand input: fixed colors, typography, design direction, logos, reference images. May be absent.
3. `<PROJECT_ROOT>/design-system/references/` — visual reference analyses (raw assets, if any, live in `project-config/references/`).
4. `creativity-rules.md` — the creative guardrails.
5. `<PROJECT_ROOT>/project-state.md` — mode and approved decisions.

## Outputs

1. `<PROJECT_ROOT>/design-system/design-direction.md` — the approved direction: concept statement, mood, inspiration (principles, not copies), and the decision log.
2. `<PROJECT_ROOT>/design-system/tokens.*` — the design tokens implemented as CSS variables and/or the Tailwind theme: colors, semantic colors, typography, spacing, radius, shadows, borders, container widths, grid, motion durations/easings (motion details live in skill 06; tokens here define the raw values).
3. `<PROJECT_ROOT>/design-system/components.md` — component-level token recipes: buttons, forms, cards, icons, image treatment.
4. Updated `project-state.md` with the approved direction recorded in the decision log.

## Responsibilities

1. Derive **three candidate design directions** from the discovery result (see *Design Directions*).
2. Present the directions with a Design Decision Protocol entry for each.
3. Implement the approved (or modified, or regenerated) direction as tokens.
4. Define the full token set: brand direction, color palette, semantic colors, typography, font hierarchy, spacing scale, container widths, grid system, border radius, shadows, borders, buttons, forms, cards, icons, image treatment, section spacing, visual hierarchy, responsive rules, animation principles.
5. Keep tokens and design direction stable — later skills may only consume them, never redefine them.
6. Respect the creativity rules: no generic template aesthetics; creativity must serve communication, hierarchy, brand, or interaction.

## Execution Workflow

### Phase 1 — Analyze Discovery
1. Read `content/discovery.ts` and the reference analyses.
2. Read `project-config/project-config.md` **first**: the configured colors, typography, design direction, logos, and references are the base input — treat them as user decisions (Rule 2) that must be preserved.
3. Extract: audience, tone, industry conventions worth honoring or breaking, and any brand colors/typography that must be preserved (Rule 2 — user decisions win).
4. If a **Figma design** is the source (see `skills/19-figma-to-nuxt/skill.md`), read `.website-builder/figma-implementation.md` — the token values come from the Figma design, not from proposed directions.

### Phase 2 — Generate Three Directions
0. If a **Figma design** exists (declared in the config's Design Source or in the request), skip direction generation: present the Figma-derived token set (from `.website-builder/figma-implementation.md`) for approval in Phase 3, proposing alternates only where the Figma is silent.
1. If `project-config.md` specifies a design direction, present it as the recommended option (A) and generate two alternates around it; if it specifies colors/typography, every direction must build on them, not replace them.
2. Otherwise, produce three distinct, concrete directions. The names are derived from the discovery — e.g.:
   - **A. Premium Minimal** — restrained, type-led, generous negative space.
   - **B. Modern Corporate** — structured, trustworthy, clean grids.
   - **C. Creative Editorial** — asymmetric, expressive, editorial layouts.
3. Each direction includes: 1-paragraph concept, color story, type pairing, layout character, image treatment, motion character (brief), and 3 example composition ideas. Mark which of the user's reference principles (from `project-config/references/` or discovery) each direction honors.

### Phase 3 — Present and Get Approval
1. Present the three directions using the **Design Decision Protocol** (Decision / Reason / Alternatives / Recommendation).
2. In `assisted`/`manual` mode: ask the user to pick, modify, or request regeneration (offer to re-derive directions if none fit).
3. In `autonomous` mode: pick the strongest fit, present it as the decision, and continue (still recorded for later approval).

### Phase 4 — Define Tokens
1. Encode the approved direction as tokens. Every value must be tokenized — no magic numbers later:
   - **Color:** start from the configured palette in `project-config.md` (fixed brand colors stay verbatim); derive semantic colors (background, surface, text, text-muted, border, primary, secondary, accent, success, warning, danger, focus) with contrast-verified pairings.
   - **Typography:** type scale with roles (display, h1–h4, body, small, caption), weights, line heights, letter spacing; font loading strategy noted (full definitions in 16/17).
   - **Spacing:** spacing scale (e.g., 4/8/12/16/24/32/48/64/96/128) and section-spacing tokens.
   - **Layout:** container widths (with breakpoints), grid columns, gutters.
   - **Shape:** radius scale, shadow scale, border widths.
   - **Motion:** duration/easing tokens (raw values; patterns in skill 06).
2. Ensure dark-mode-ready structure if the direction includes it (semantic tokens).

### Phase 5 — Define Component Recipes
1. In `design-system/components.md`, define the recipes for: buttons (variants, sizes, states), forms (inputs, labels, errors, focus), cards (variants), icons (set, size, stroke), image treatment (radius, aspect, overlay, gradient).
2. These recipes are prescriptive — widgets (05) implement them, never redesign them.

### Phase 6 — Document Direction
1. Write `design-direction.md`: concept, mood board description (textual, principle-based), typography rationale, color rationale, do/don't list, and the decision log.
2. Add an explicit note on which reference principles were adopted (and which were intentionally not).

### Phase 7 — Verify and Report
1. Verify tokens are complete and internally consistent (every referenced token exists; contrast pairs pass).
2. Update `project-state.md` with status `COMPLETED` and the approved direction in the decision log.

## Design Decision Protocol

For each major decision (direction, palette, type pairing, layout character), present:

```text
Decision:
<what you chose>

Reason:
<why it fits the discovery and brand>

Alternative:
<another viable option>

Recommendation:
<your recommended choice>
```

## Decision Rules

- **User decisions win:** brand colors, logos, and typography provided by the user — including everything in `project-config/project-config.md` — are preserved even if a direction would prefer otherwise.
- **Figma wins visually:** when a Figma design is the source, its values define the tokens (the Figma is the visual source of truth, per the `19-figma-to-nuxt` skill); fixed brand values from the config remain for anything the Figma does not specify.
- **Derived, not defaulted:** the three directions must be generated from the discovery, never copy-pasted generic templates. The example names are illustrative only.
- **Regeneration is cheap:** if the user rejects all three directions, generate three new ones rather than forcing a fit.
- **Consistency over cleverness:** token values must be systematic (scale-based), not a collection of one-off values.
- **Creativity rules apply:** consult `creativity-rules.md`; if a direction is "generic SaaS", it is not offered.

## User Interaction

- Present directions visually and verbally (color swatches, type samples, layout sketches in markdown where possible).
- Ask for: pick A/B/C, modify any direction, or regenerate.
- In `assisted`/`manual` mode, never implement a direction without approval. In `autonomous` mode, implement the recommendation and log it.

## Implementation Rules

- Every styling value in later code MUST come from these tokens. This is Cross-Skill Rule 1.
- Semantic tokens (not raw palette) are used for component styling so theme changes don't ripple.
- Tokens are implemented in both CSS variables (for runtime/utility use) and the Tailwind theme (for utility classes) — single source, generated from one definition where practical.
- No business-specific design, images, or copy in this skill.

## Quality Requirements

- All required token categories are defined (see *Responsibilities*).
- Tokens are referenced, not duplicated: no hard-coded colors/spacing anywhere in the system.
- Color pairs meet WCAG contrast (AA for text) — verified programmatically where possible.
- The direction is distinctive, not template-like (creativity rules pass).
- The decision log records the approved direction and all modifications.

## Validation

- `design-direction.md`, `tokens.*`, and `components.md` exist and are non-empty.
- Token completeness check: every token referenced in `components.md` exists in `tokens.*`.
- Contrast check on text/background pairs.
- The direction honors all user-provided brand constraints.

## Completion Criteria

- Approved direction recorded in project state.
- Complete token set implemented and documented.
- Component recipes defined.
- Creativity rules respected.

## Failure / Recovery Rules

- **User rejects everything:** regenerate three new directions from the same discovery; do not fall back to defaults.
- **Token inconsistencies:** run the completeness/contrast checks; fix token definitions; re-run checks until clean.
- **Brand asset conflict:** if a token contradicts a user-provided brand value, the user value wins and is recorded.
- **Late brand info:** update discovery, re-derive directions, mark 04/05/06/07–12 `NEEDS_REVISION`.
