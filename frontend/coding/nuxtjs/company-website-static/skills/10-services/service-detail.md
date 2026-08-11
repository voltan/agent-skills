# Skill 10b — Service Detail (`/services/[slug]`)

Companion specification to skill 10 for the individual service page. Loaded and executed together with skill 10 (or on its own when only detail pages need (re)work).

## Purpose

Design and implement the **individual service page** at `/services/[slug]`, dynamically selecting the structure per service — never a fixed template.

## Preconditions

- Skill 10 listing page exists; `/services/[slug]` route scaffolded.
- `content/services.ts` contains full service records.
- Skills 03–06 completed; mode known.

## Inputs

1. Design system, `widget-catalog.md`, `creativity-rules.md`.
2. `content/services.ts` — the specific service record (features, process, industries, technologies, case studies, FAQ).
3. `project-config/project-config.md` — brand input (service content notes, logo).
4. `content/site.ts`, `project-state.md`.

## Outputs

1. `<PROJECT_ROOT>/pages/services/[slug].vue` — the dynamic service detail page.
2. Updated `project-state.md`.

## Potential Structure (dynamically selected)

```text
Hero                 (service name, tagline, CTA)
Problem              (why this service exists)
Solution             (what the service delivers)
Capabilities         (FeatureGrid)
Features             (FeatureList)
Benefits             (outcomes)
Process              (Steps/ProcessTimeline)
Industries           (IndustriesGrid)
Technologies         (logo/tech strip)
Case Studies         (CaseStudies — real only)
FAQ                  (Accordion)
CTA
```

**Selection rules:** include a section only when the service record has real (or placeholder-marked) content for it. Case studies only with real case studies. FAQ only when questions exist. Order follows the buyer's journey: context → solution → proof → action.

## Execution Workflow

### Phase 1 — Analyze the Service
1. Read the specific service record from `content/services.ts`.
2. Cross-check with `project-config.md` content notes if the user described the service there.
3. Map its fields to the candidate sections; drop sections without content.

### Phase 2 — Propose Architecture
1. Produce the section/widget list with rationale (Widget Selection Protocol).
2. Ensure the detail page differs meaningfully from the listing page (no duplicated composition).

### Phase 3 — Approve
1. Per mode: present and approve (assisted/manual) or record (autonomous).

### Phase 4 — Implement
1. Build `pages/services/[slug].vue` with dynamic content binding (use the slug/params to select the record from `content/services.ts`).
2. Add breadcrumbs (Breadcrumbs widget) and links to related services.
3. Apply animations; responsive + accessibility; self-QA.

### Phase 5 — Report
1. Update `project-state.md`.

## Decision Rules

- **Content-driven structure:** sections appear only when their content exists; missing content = marked placeholder, not fabricated copy.
- **No duplication:** never duplicate the listing page inside a detail page.
- **Design continuity:** tokens, recipes, rhythm, and widget language match the rest of the site.
- **Static-only:** detail pages are prerendered statically; no dynamic fetching at runtime.

## User Interaction

- Propose the per-service structure for approval per mode.
- For services with sparse content, ask whether to expand copy now (skill 13 territory) or keep placeholders.

## Implementation Rules

- Dynamic route reads the record from `content/services.ts` by slug; unknown slugs render a 404 (or documented fallback).
- One `h1` per page; breadcrumb + related links accessible.
- All content placeholder-separated.

## Quality Requirements

- The page structure matches the service's actual content (no empty sections).
- Long-form sections (FAQ, case studies) are readable and accessible.
- Prerendering includes all service slugs (SEO).

## Validation

- Every slug in `content/services.ts` renders without errors at build.
- A sample detail page passes responsive/accessibility smoke checks.
- No fabricated content.

## Completion Criteria

- Detail page implemented with approved per-service structure.
- All slugs prerendered.
- `project-state.md` updated.

## Failure / Recovery Rules

- **Empty service record:** render with placeholders and flag; do not invent capabilities.
- **Missing slug:** verify the record list; keep the route but ensure 404 behavior for unknown slugs.
- **Structure rejected:** revise and re-present per mode.
