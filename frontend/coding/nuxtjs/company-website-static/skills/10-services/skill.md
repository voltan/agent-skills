# Skill 10 — Services (Listing)

## Purpose

Design and implement the **Services listing page** at `/services`, dynamically composing the appropriate widgets for the company's actual service offering.

## Role

You are a **UX/UI Architect and Information Designer**. You structure the service offering so visitors can scan, understand, and navigate to detail pages.

## Preconditions

- Skills 03–06 completed.
- `content/services.ts` and `content/site.ts` exist with placeholder service data.
- Mode known.

## Inputs

1. Design system (`design-direction.md`, `tokens.*`, `components.md`).
2. `content/discovery.ts` — services, categories, industries.
3. `content/services.ts` — service records (slug, title, summary, category, features).
4. `widget-catalog.md`, `creativity-rules.md`.
5. `project-config/project-config.md` — brand input (services/content notes, logo).
6. `project-state.md`.

## Outputs

1. `<PROJECT_ROOT>/pages/services/index.vue` — the listing page.
2. Service detail route support (see `service-detail.md`) — route and content wiring.
3. Any new catalog widgets (recorded).
4. Updated `project-state.md`.

## Responsibilities

1. Compose the listing from the service catalog data.
2. Support service categories when the offering is grouped.
3. Link every card to its detail page (`/services/[slug]`).
4. Include supporting sections that actually help: benefits, process, industries, CTA.
5. Implement per the Widget Selection Protocol and design system.

## Potential Sections

- Hero (page intro: "Our Services")
- Service categories (grouping/navigation)
- Service cards (ServicesGrid of ServiceCards)
- Benefits (why work with us)
- Process (ProcessTimeline/Steps)
- Industries (IndustriesGrid)
- CTA

## Execution Workflow

### Phase 1 — Analyze
1. Read `content/services.ts`: how many services, categories, and what each promises.
2. Cross-check with `project-config.md` content notes (services the user listed are authoritative).
3. Decide the listing presentation (grid, grouped by category, bento).

### Phase 2 — Propose Architecture
1. Widget sequence with rationale (e.g., HeroMinimal → ServicesGrid (grouped) → Benefits → ProcessTimeline → CTASection).
2. Check creativity rules.

### Phase 3 — Approve
1. Per mode: present and approve (assisted/manual) or record (autonomous).

### Phase 4 — Implement
1. Build `pages/services/index.vue`; bind content from `content/services.ts`.
2. Ensure every ServiceCard links to `/services/[slug]` with correct slug.
3. Implement the detail route wiring (see `service-detail.md`) so links resolve.

### Phase 5 — Self-QA and Report
1. Responsive/accessibility/perf smoke checks; fix issues.
2. Update `project-state.md`.

## Decision Rules

- **Data-driven:** the page renders from `content/services.ts` — adding/removing a service must not require editing the page.
- **No invented services:** placeholders only; never fabricate offerings.
- **Category-aware:** if discovery lists categories, group by them; otherwise a flat grid.
- **Design continuity:** same tokens/rhythm as other pages.

## User Interaction

- Propose listing presentation and section set; approve per mode.
- Ask whether the company wants category grouping (if ambiguous).

## Implementation Rules

- Cards are `article` elements with descriptive links (service title).
- Heading hierarchy: one `h1`, sections `h2`.
- Content fully bound from content sources (Rule 4).

## Quality Requirements

- Listing is scannable: titles, summaries, and links clear at a glance.
- Detail links all resolve to valid slugs.
- No hard-coded service data in components.

## Validation

- `/services` renders; every card links to an existing slug.
- Static build passes; responsive smoke test passes.
- Placeholder audit clean (all marked).

## Completion Criteria

- Listing page implemented with approved architecture.
- Service detail routing wired.
- `project-state.md` updated.

## Failure / Recovery Rules

- **No services in content:** use placeholder records (clearly marked) so the page structure can be validated; flag for skill 13.
- **Broken slugs:** verify `content/services.ts` slugs match route expectations before completion.
- **Architecture rejected:** revise and re-present per mode.
