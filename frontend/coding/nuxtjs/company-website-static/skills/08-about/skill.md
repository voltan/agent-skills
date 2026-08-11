# Skill 08 — About

## Purpose

Design and implement the **About page** — the company's story, identity, and credibility. Sections are selected according to the actual company requirements from discovery, not a fixed template.

## Role

You are a **UX/UI Architect and Narrative Designer**. You turn the company's story into a page structure that builds trust.

## Preconditions

- Skills 03–06 completed.
- `content/about.ts` and `content/site.ts` exist with placeholder content.
- Mode known.

## Inputs

1. Design system (`design-direction.md`, `tokens.*`, `components.md`).
2. `content/discovery.ts` — company story, values, team, certifications, partners availability.
3. `content/about.ts`, `content/site.ts`.
4. `widget-catalog.md`, `creativity-rules.md`.
5. `project-config/project-config.md` — brand input (company facts, content notes, logo).
6. `project-state.md`.

## Outputs

1. `<PROJECT_ROOT>/pages/about.vue` — the About page.
2. Any new catalog widgets required (recorded).
3. Updated `project-state.md`.

## Responsibilities

1. Analyze discovery: what does the company want visitors to believe after reading this page?
2. Select only the sections that fit the actual company (see *Potential Sections*).
3. Compose widgets per the Widget Selection Protocol.
4. Implement with tokens, recipes, and animations.
5. Ensure responsive and accessible behavior; self-QA.

## Potential Sections

The following sections are available — **select only what the company needs**:

- Hero (page-level intro)
- Company Introduction
- Mission
- Vision
- Values
- Story
- Timeline (company history)
- Statistics
- Leadership
- Team
- Certifications
- Partners
- CTA (closing action)

Select by: company size (team section only with real team data), industry (certifications for regulated industries), available content (story/timeline only when real milestones exist — otherwise placeholder gaps are flagged, not invented).

## Execution Workflow

### Phase 1 — Analyze
1. Read discovery + about content.
2. Read `project-config.md` content notes (company story, values, certifications) and use the logo from `project-config/brand/` where the design calls for it.
3. Decide the narrative: origin → values → proof (team/stats/certs) → CTA, adapted to the company.

### Phase 2 — Propose Architecture
1. Produce the ordered widget architecture with rationale (Widget Selection Protocol).
2. Check creativity rules: avoid the generic formula; vary composition.

### Phase 3 — Approve
1. Per mode: present architecture for approval (assisted/manual) or record it (autonomous).

### Phase 4 — Implement
1. Build `pages/about.vue` from layout primitives + widgets; bind content from `content/about.ts`.
2. Apply animations (entrance reveals, story-friendly motion); reduced-motion safe.

### Phase 5 — Self-QA and Report
1. Responsive/accessibility/perf smoke checks; fix issues.
2. Update `project-state.md`.

## Decision Rules

- **Real-content discipline:** team photos, stats, certifications, and partner logos must be real or flagged as placeholders — never fabricated.
- **Design continuity:** the About page belongs to the same design system as the homepage (same tokens, rhythm, and widget language).
- **Narrative over template:** section order follows the company's story.
- **Static-only:** no backend, no dynamic loading.

## User Interaction

- Propose the section set and order; ask for approval per mode.
- Ask which sections to include if discovery is ambiguous (e.g., "include a Team section?").
- Accept requests to add/remove sections; re-present before implementing per mode.

## Implementation Rules

- Heading hierarchy: one `h1` in the hero, `h2` per section, `h3` in cards/widgets.
- Team/stat/cert widgets follow their catalog accessibility specs.
- All content placeholder-separated (Rule 4).

## Quality Requirements

- The page reads as a coherent story, not a checklist of sections.
- Only relevant sections appear; no empty placeholder sections that mislead.
- Consistency with homepage design language.

## Validation

- Static build passes.
- No invented facts: placeholders are marked `[Placeholder: …]`.
- Responsive smoke test passes.
- Heading hierarchy valid.

## Completion Criteria

- About page implemented with approved architecture.
- Content bound from `content/about.ts`.
- `project-state.md` updated.

## Failure / Recovery Rules

- **No company data:** build with explicit placeholders and flag the gaps; do not invent a story.
- **Section requested without content:** include the section with placeholder content and mark it in the content-replacement list (skill 13 will handle).
- **Architecture rejected:** revise and re-present per mode.
