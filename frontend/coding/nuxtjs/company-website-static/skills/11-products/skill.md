# Skill 11 — Products (Listing)

## Purpose

Design and implement the **Products listing page** at `/products`, supporting multiple visual presentations chosen to fit the company's product set and brand.

## Role

You are a **UX/UI Architect and Product Showcase Designer**. You present products so visitors can scan, compare, and dive into details.

## Preconditions

- Skills 03–06 completed.
- `content/products.ts` and `content/site.ts` exist with placeholder product data.
- Mode known.

## Inputs

1. Design system (`design-direction.md`, `tokens.*`, `components.md`).
2. `content/discovery.ts` — product set, categories.
3. `content/products.ts` — product records (slug, name, tagline, category, image, tags).
4. `widget-catalog.md`, `creativity-rules.md`.
5. `project-state.md`.

## Outputs

1. `<PROJECT_ROOT>/pages/products/index.vue` — the listing page.
2. Product detail route wiring (see `product-detail.md`).
3. Any new catalog widgets (recorded).
4. Updated `project-state.md`.

## Responsibilities

1. Choose the presentation that fits the product set (see *Presentations*).
2. Render all products from `content/products.ts` (data-driven).
3. Link every product to `/products/[slug]`.
4. Support categories when present.
5. Implement per the Widget Selection Protocol and design system.

## Presentations (choose the right one)

- **Grid** — uniform ProductCard grid. Default for 4–12 comparable products.
- **Bento** — BentoGrid with a featured large tile. For a flagship product or rich visuals.
- **Editorial** — editorial list with oversized type and imagery. For few, premium products.
- **Featured product** — a large featured product section above the grid. For a clear market leader.
- **Product categories** — grouped sections by category (with optional filter).

Selection is based on product count, visual richness, and brand tone — not preference for a fancy layout (creativity rules).

## Potential Sections

- Hero (page intro)
- Category navigation (if grouped)
- Product presentation (per *Presentations*)
- Comparison teaser (optional, if products invite comparison)
- CTA

## Execution Workflow

### Phase 1 — Analyze
1. Read `content/products.ts`: count, categories, and visual material available.
2. Choose the presentation; justify it.

### Phase 2 — Propose Architecture
1. Widget sequence with rationale; check creativity rules.

### Phase 3 — Approve
1. Per mode: present and approve (assisted/manual) or record (autonomous).

### Phase 4 — Implement
1. Build `pages/products/index.vue`; bind content from `content/products.ts`.
2. Wire `/products/[slug]` detail routes.
3. Apply animations; responsive + accessibility; self-QA.

### Phase 5 — Report
1. Update `project-state.md`.

## Decision Rules

- **Data-driven:** product list changes never require page edits.
- **Presentation fits content:** a single product does not get an empty grid; sparse sets get editorial/featured treatment.
- **No invented products:** placeholders only.
- **Design continuity:** tokens, recipes, rhythm, widget language.

## User Interaction

- Propose the presentation with a Design Decision Protocol entry (Decision / Reason / Alternative / Recommendation).
- Approve per mode.

## Implementation Rules

- Product images: `alt` from content; aspect ratios from the design system (image treatment tokens).
- Cards are `article` elements with descriptive links.
- Content fully bound (Rule 4).

## Quality Requirements

- Presentation genuinely fits the product set.
- All product links resolve; all slugs prerender.
- No hard-coded product data.

## Validation

- `/products` renders; links resolve.
- Static build + responsive smoke test pass.
- Placeholder audit clean.

## Completion Criteria

- Listing implemented with approved presentation.
- Detail routing wired.
- `project-state.md` updated.

## Failure / Recovery Rules

- **No product data:** placeholder records (marked) so structure is testable; flag for skill 13.
- **Broken images:** verify asset references before completion.
- **Presentation rejected:** re-propose an alternative presentation per mode.
