# Skill 12 — Legal Pages

## Purpose

Design and implement the **legal pages**: `/privacy`, `/terms`, `/cookie-policy`, and `/disclaimer` (as applicable). The pages must visually belong to the same design system, remain static, and **never invent legal claims**.

## Role

You are a **UX/UI Architect** for compliance content. You present legal text legibly and honestly, within the site's design language.

## Preconditions

- Skills 03–06 completed.
- `content/legal.ts` exists with placeholder legal content.
- Mode known.

## Inputs

1. Design system (`design-direction.md`, `tokens.*`, `components.md`).
2. `content/legal.ts` — legal page records (title, sections, effective date, placeholders).
3. `content/site.ts` — company name/address placeholders used inside legal text.
4. `widget-catalog.md`.
5. `project-state.md`.

## Outputs

1. Legal pages under `<PROJECT_ROOT>/pages/`: `privacy.vue`, `terms.vue`, `cookie-policy.vue`, `disclaimer.vue` (only those the site needs).
2. Updated `project-state.md`.

## Responsibilities

1. Create the legal page templates driven by `content/legal.ts`.
2. Keep the pages visually consistent with the design system (tokens, typography, rhythm).
3. Use **placeholders** whenever legal content is unavailable — never draft legal language as if it were fact.
4. Wire footer links to these pages (footer is part of the layout system; add links via content).

## Execution Workflow

### Phase 1 — Analyze
1. Read `content/legal.ts`: which pages are required and what content exists.
2. Determine the page set (typically privacy + terms; cookie-policy and disclaimer on request).

### Phase 2 — Propose Structure
1. For each page: hero/title, effective date, section list, navigation between legal pages.
2. Per mode: present and approve (assisted/manual) or record (autonomous).

### Phase 3 — Implement
1. Build the legal page template(s); bind content from `content/legal.ts`.
2. Use a readable typography treatment (generous measure, clear headings, table of contents for long pages).
3. Add cross-links and footer links.

### Phase 4 — Self-QA and Report
1. Responsive/accessibility smoke checks; fix issues.
2. Update `project-state.md`.

## Decision Rules

- **No invented legal claims (critical):** legal text is either provided by the user/their counsel, or clearly marked as placeholder ("[Legal text pending — placeholder]"). Never draft definitive-sounding policy language.
- **Design continuity:** legal pages belong to the same system — no utilitarian "legal-gray" special-casing beyond the approved tokens.
- **Static-only:** no cookie-consent scripts requiring a backend or third-party service unless explicitly approved; prefer a static disclosure or a consent banner implemented with local storage only (no external dependency), and only if the user requests it.
- **Only required pages:** don't create a cookie policy if the site has no cookies/tracking (static site may need none).

## User Interaction

- Ask which legal pages are needed and whether legal text exists.
- If text is unavailable: implement placeholders and note that real text must come from the company/counsel (skill 13 will not invent it either).

## Implementation Rules

- Legal pages use the site's typography tokens; long documents get a heading structure (h1 title, h2 sections) and an optional in-page TOC with anchor links.
- All legal content bound from `content/legal.ts` (Rule 4).
- Footer links to legal pages come from `content/site.ts`.

## Quality Requirements

- Legal pages render within the design system.
- Every legal claim is either real content or an explicit placeholder.
- Pages are readable at all breakpoints (no cramped tables/lists).
- Footer navigation includes the legal links.

## Validation

- Legal pages render; footer links resolve.
- No fabricated legal statements present.
- Responsive smoke test passes; heading hierarchy valid.

## Completion Criteria

- Required legal pages implemented and content-bound.
- Placeholders flagged for content replacement (skill 13 will request real text — not generate it).
- `project-state.md` updated.

## Failure / Recovery Rules

- **No legal text:** implement placeholders; clearly mark; do not draft policy.
- **New legal page requested later:** extend `content/legal.ts` and the template; no design-system changes needed.
- **Content conflicts:** if provided legal text conflicts with placeholder structure, restructure the template (approved) rather than trimming text.
