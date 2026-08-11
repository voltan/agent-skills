# Skill 11b — Product Detail (`/products/[slug]`)

Companion specification to skill 11 for the individual product page. Loaded and executed together with skill 11 (or on its own when only detail pages need (re)work).

## Purpose

Design and implement the **individual product page** at `/products/[slug]`, with the final structure depending on the product itself.

## Preconditions

- Skill 11 listing page exists; `/products/[slug]` route scaffolded.
- `content/products.ts` contains full product records.
- Skills 03–06 completed; mode known.

## Inputs

1. Design system, `widget-catalog.md`, `creativity-rules.md`.
2. `content/products.ts` — the specific product record.
3. `project-config/project-config.md` — brand input (product content notes, logo).
4. `content/site.ts`, `project-state.md`.

## Outputs

1. `<PROJECT_ROOT>/pages/products/[slug].vue` — the dynamic product detail page.
2. Updated `project-state.md`.

## Potential Structure (depends on the product)

```text
Product Hero      (name, tagline, key CTA, product visual)
Overview          (what it is, who it's for)
Key Features      (FeatureGrid)
Screenshots       (gallery/lightbox of real screenshots)
Capabilities      (deeper capability grid)
Benefits          (outcomes)
Use Cases         (scenarios)
Architecture      (diagram/section — only when meaningful)
Technology        (tech stack strip)
Comparison        (Comparison — real data only)
FAQ               (Accordion)
CTA
```

**Selection rules:** include a section only when the product record has content for it. Screenshots only from real images. Comparison only with real competitive data. Architecture/Technology only when the product warrants it. Order follows: what it is → what it does → proof → action.

## Execution Workflow

### Phase 1 — Analyze the Product
1. Read the specific product record.
2. Cross-check with `project-config.md` content notes if the user described the product there.
3. Map fields to candidate sections; drop sections without content.

### Phase 2 — Propose Architecture
1. Section/widget list with rationale (Widget Selection Protocol).
2. Ensure the page differs from the listing page and from service pages.

### Phase 3 — Approve
1. Per mode: present and approve (assisted/manual) or record (autonomous).

### Phase 4 — Implement
1. Build `pages/products/[slug].vue`; bind content by slug from `content/products.ts`.
2. Add breadcrumbs + related products links.
3. Apply animations (product reveal, gallery); responsive + accessibility; self-QA.

### Phase 5 — Report
1. Update `project-state.md`.

## Decision Rules

- **Content-driven structure:** no empty sections; missing content = marked placeholder.
- **No invented specs:** features, screenshots, comparison, and tech claims are placeholders until real (never fabricated — compliance-sensitive).
- **Prerendered static:** all product slugs prerender; no runtime fetching.
- **Design continuity:** tokens, recipes, rhythm, widget language.

## User Interaction

- Propose the per-product structure for approval per mode.
- Ask whether the user wants a comparison table before including it (real data required).

## Implementation Rules

- Dynamic route selects the record by slug; unknown slug → 404 (or documented fallback).
- Screenshot gallery: accessible controls, alt text, lightbox with keyboard support.
- One `h1` per page; breadcrumbs; content placeholder-separated.

## Quality Requirements

- Structure matches the product's actual content.
- Screenshots/tech/comparison sections are honest (placeholders marked).
- All product slugs prerender cleanly.

## Validation

- Every slug renders without errors at build.
- Sample detail page passes responsive/accessibility smoke checks.
- No fabricated product claims.

## Completion Criteria

- Detail page implemented with approved structure.
- All slugs prerendered.
- `project-state.md` updated.

## Failure / Recovery Rules

- **Empty product record:** placeholders + flagged gaps; never invent features.
- **Missing assets:** keep placeholders; list for skill 13.
- **Structure rejected:** revise and re-present per mode.
