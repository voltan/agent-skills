# Skill 13 — Content Replacement

## Purpose

Replace **all placeholder content** with **real content** across the website — without breaking the design. This skill is the point where "structure first, design second, content later" pays off: content is centralized in structured sources, so replacement is data work, not UI surgery.

## Role

You are a **Content Strategist and Copy Editor**. You inventory placeholders, gather real content, adapt copy to the design's constraints, and keep the UI intact.

## Preconditions

- Pages exist (skills 07–12 completed) with placeholder content in `content/*.ts`.
- `content/discovery.ts` provides the facts real content must be consistent with.
- Mode known.

## Inputs

1. `<PROJECT_ROOT>/content/*.ts` — all content sources (`site.ts`, `homepage.ts`, `about.ts`, `services.ts`, `products.ts`, `legal.ts`, `discovery.ts`).
2. `<PROJECT_ROOT>/design-system/design-direction.md` — tone and voice expectations.
3. `<PROJECT_ROOT>/project-config/project-config.md` (+ `brand/`) — content notes the user already provided (company facts, services/products, contact data) and logo/brand asset files.
4. Real content provided by the user (text, images, contact data, legal text, metrics) — or explicit authorization to generate copy.
5. `<PROJECT_ROOT>/project-state.md`.

## Outputs

1. Updated `content/*.ts` with real content (placeholders removed; remaining gaps explicitly listed).
2. `<PROJECT_ROOT>/docs/content-report.md` — what was replaced, what is still missing, any length-driven design adjustments.
3. Any approved layout adjustments made to preserve integrity (documented; markup changed only when necessary).
4. Updated `project-state.md`.

## Responsibilities

1. **Identify all placeholder content** — scan content sources for `[Placeholder: …]` markers and Lorem ipsum; also scan pages/components for hard-coded placeholder text (violations of Rule 4, fixed here).
2. **Ask for real content where necessary** — company facts, services, products, team, stats, testimonials, legal text, contact details, images.
3. **Preserve layout integrity** — replacement copy must fit the designed containers; adjust text length intelligently rather than redesigning.
4. **Adjust text length intelligently** — truncation, rewording, and responsive wrapping rules; never overflow silently.
5. **Generate SEO-aware copy only when explicitly authorized** — otherwise request it from the user.
6. **Avoid breaking the design because of content length** — see *Length Rules*.
7. **Identify missing content** — every unfilled placeholder is reported as a gap with its location and impact.

## Execution Workflow

### Phase 1 — Inventory Placeholders
1. Grep all `content/*.ts` and pages/components for placeholder markers.
2. Produce a categorized inventory: company name/brand, contact, homepage sections, services, products, team, stats, testimonials, legal, images.
3. Note any hard-coded text in components (Rule 4 violations) — these move into content sources.

### Phase 2 — Gather Real Content
1. Start from `project-config.md` content notes — everything filled there (company facts, services, products, contact data) is already-provided content; do not re-ask for it.
2. If a **Figma design** is the source, mirror its copy and text lengths (heading/paragraph/CTA sizes) so the composition stays intact — see `skills/19-figma-to-nuxt/skill.md`, section 25 (realistic placeholder lengths).
3. Request the remaining content from the user in batches (contact info → company intro → services/products → proof content → legal).
3. If the user delegates copywriting, generate copy **only with explicit authorization**; otherwise mark the item `awaiting content`.
4. Collect/verify images and assets — logos come from `project-config/brand/`, photos/screenshots from the user — and reference them in content sources.

### Phase 3 — Replace
1. Replace placeholders in `content/*.ts` only. Components/pages must not require edits (if they do, that's a Rule 4 violation — fix the source binding).
2. Keep values typed: numbers stay numbers, URLs stay URLs, alt text stays meaningful.
3. Keep text lengths within the design's constraints (see *Length Rules*); if a piece of copy cannot fit, request a shorter version or (approved) adjust the design token for that container.

### Phase 4 — Preserve Design Integrity
1. After replacement, render and check: no overflow, no broken layouts, no orphaned headings, images proportionally correct.
2. Fix only what's broken — prefer copy adjustments over layout changes; any layout change must be approved (per mode) and recorded.

### Phase 5 — Verify and Report
1. Re-scan: zero placeholders remain, or the remaining ones are listed as explicit gaps.
2. Write `docs/content-report.md`.
3. Update `project-state.md` (status `COMPLETED` or `NEEDS_REVISION` with the gap list).

## Length Rules (preserve layout integrity)

- **Titles:** keep within the display type's designed max-width; prefer shortening over shrinking type.
- **Descriptions:** respect line-clamp limits only where designed (cards); full text on detail pages.
- **Stats/metrics:** numbers must be real; unit strings short.
- **Images:** aspect ratios fixed by tokens; swap image files, never stretch.
- **Any overflow detected** in QA is fixed here by copy adjustment first — the design tokens are the constraint, not the variable.

## Decision Rules

- **Real over generated:** user-provided content wins; AI-generated copy only with explicit authorization.
- **Never invent proof:** testimonials, stats, certifications, case studies, team members, and legal text are never fabricated. Missing → gap.
- **Truth in placeholders:** if the user cannot provide content, keep a clear placeholder and mark it as a gap — do not silently leave believable fake copy.
- **Design is the constraint:** copy adapts to the approved design, not vice versa (unless the user approves a design change).
- **SEO-aware copy:** title/meta/description copy follows the SEO skill's (16) conventions; generate only when authorized.

## User Interaction

- Ask for content in small, prioritized batches; accept any format (text, files, links).
- Confirm whether AI copywriting is authorized. If yes, generate drafts and request review; if no, mark items as gaps.
- In `assisted`/`manual` mode, get approval before any layout adjustment. In `autonomous` mode, prefer copy-only fixes and log any layout change.

## Implementation Rules

- All replacements happen in `content/*.ts`; components stay content-agnostic.
- No hard-coded text may remain in components/pages after this skill.
- Keep the typed interfaces intact; extend them only if a field is genuinely needed (approved).
- Do not touch design tokens, layout primitives, or widget logic unless a documented, approved exception exists.

## Quality Requirements

- Zero remaining placeholders except explicitly reported gaps.
- All copy is consistent with discovery (facts, tone, terminology).
- No layout regressions from replacement (overflow, breakage, distorted images).
- Content report is accurate and actionable.

## Validation

- Placeholder scan returns only documented gaps.
- Static build passes; pages render with real copy at 3 breakpoints.
- Fact check: stats, names, URLs, contact details match what the user provided.
- No invented testimonials/stats/legal text.

## Completion Criteria

- All available content replaced; gaps documented in `docs/content-report.md`.
- Design integrity preserved (or approved adjustments recorded).
- `project-state.md` updated.

## Failure / Recovery Rules

- **User provides nothing:** keep placeholders, report gaps; never invent.
- **Copy overflow:** shorten copy or get approval to adjust the container; never stretch type.
- **Rule 4 violation found (hard-coded text):** move it into the content source and fix the binding before replacing.
- **Late content arrives:** update `content/*.ts`, re-verify design integrity, mark QA skills `NEEDS_REVISION` only if layouts were affected.
