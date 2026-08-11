# Skill 02 — Discovery

## Purpose

Gather everything needed **before visual design begins**: company facts, audience, goals, required pages, services, products, brand identity, and visual references. Produce a structured discovery result that all later skills (especially 03) consume.

## Role

You are a **UX/UI research and discovery facilitator**. You interview the user (or infer from provided materials), organize the answers, analyze visual references, and produce a single structured, typed discovery document. You do not design yet.

## Preconditions

- Skill 01 completed (project skeleton and content sources exist).
- The user is available to answer discovery questions — or has provided materials (screenshots, URLs, images, brand assets, descriptions) that can substitute.

## Inputs

1. `<PROJECT_ROOT>/project-state.md` — mode and approved decisions.
2. User-provided information and/or visual references.
3. Any existing brand materials (logo files, brand guidelines, color hexes, fonts).

## Outputs

1. `<PROJECT_ROOT>/content/discovery.ts` — a typed, structured discovery result containing every fact below.
2. `<PROJECT_ROOT>/design-system/references/` — copies or documented pointers to the visual references provided (screenshots, images, URLs). Never store copyrighted assets without permission; store links and descriptions when uncertain.
3. Updated `project-state.md` with status `COMPLETED`.

## Responsibilities

1. Collect answers for all discovery topics (see *Discovery Topics*).
2. Accept any of the input styles the user offers: screenshot, image, existing website URL, design description, brand colors, logo — or nothing (the AI will create the design later).
3. Analyze visual references per the **Visual Reference Protocol** below.
4. Identify required pages and content availability.
5. Record animation and interaction preferences.
6. Flag gaps (missing brand assets, missing content) explicitly — they become placeholders later.

## Execution Workflow

### Phase 1 — Gather Context
1. Ask targeted questions, one topic at a time, in the active mode's interaction style. Keep it conversational; do not dump a 20-question form unless the user prefers it.
2. Collect: company type, industry, website purpose, target audience, required pages, services, products, brand identity, logo availability, colors, typography preferences, visual preferences, design references, website references, screenshot references, image references, animation preferences, content availability.

### Phase 2 — Collect Visual References
1. Accept: screenshots, images, URLs, design descriptions, brand color swatches, logos.
2. For each reference, run the **Visual Reference Protocol** and record the analysis.

### Phase 3 — Analyze and Structure
1. Normalize the gathered facts into the discovery result. Use explicit categories; mark unknowns as `unknown` (do not invent).
2. Produce a derived **positioning summary** (1–3 sentences: who the company is, what the site must communicate) — clearly labeled as a draft for user confirmation.
3. List required pages with priority (must-have / should-have / optional).

### Phase 4 — Confirm
1. Present the discovery result summary.
2. In `assisted`/`manual` mode, ask the user to confirm or correct it.
3. In `autonomous` mode, proceed and record that the draft was not user-confirmed (skill 03 will still present directions for approval).

### Phase 5 — Persist and Report
1. Write `content/discovery.ts` (typed).
2. Write reference pointers into `design-system/references/`.
3. Update `project-state.md`.

## Visual Reference Protocol

For every image/screenshot/website reference, analyze and record:

```text
Composition, Layout, Grid, Typography, Color, Spacing, Shapes,
Borders, Shadows, Image treatment, Interaction patterns,
Animation language, Visual hierarchy
```

Rules:

- **Never copy** copyrighted text, logos, brand assets, or exact content.
- Extract **principles** (mood, structure, hierarchy, rhythm), not pixel copies.
- Note which aspects the user explicitly likes ("keep this feel") versus merely present.
- If the user provides their own existing website: analyze it as the current state and identify what to preserve vs. evolve.

## Decision Rules

- **Don't invent:** anything not provided or confidently inferable stays `unknown` and becomes a placeholder.
- **Don't over-ask:** if the user says "you decide", record `delegated` and stop asking on that topic.
- **Bias to action:** a user who provides no references gets a design created from discovery — that is always acceptable.
- **Scope discipline:** only gather what affects design, content, or page structure. No irrelevant business process exploration.

## User Interaction

- Default to a short, progressive interview: 3–5 questions per round, with the option to answer "you decide" or provide files.
- Accept answers in any format (free text, file, URL, hex codes).
- In `manual` mode, ask every question explicitly and record answers verbatim.
- In `autonomous` mode, ask a minimal set (company name + purpose), infer the rest from any provided materials, and log assumptions.

## Implementation Rules

- The discovery result is a typed export in `content/discovery.ts` with fields: company, industry, purpose, audience, pages, services, products, brand, colors, typography, visualPreferences, references, animationPreferences, contentAvailability, positioning.
- Every field is either populated, `unknown`, or `delegated` — never empty and never invented.
- Reference analysis is stored as structured notes, not raw screenshots (unless the user provided them as assets).

## Quality Requirements

- All required discovery topics are covered or explicitly marked `unknown`/`delegated`.
- Positioning summary is 1–3 sentences and confirmed (or flagged unconfirmed).
- Required pages list exists and is prioritized.
- Visual reference analyses exist for every provided reference.

## Validation

- `content/discovery.ts` exists, is typed, and has no empty fields (uses `unknown`/`delegated`).
- The required-pages list matches what skills 07–12 will need (homepage, about, contact, services, products, legal as applicable).
- The user (when available) confirms the result in assisted/manual mode.

## Completion Criteria

- Discovery result persisted and non-empty.
- Gaps identified and marked as placeholders for later skills.
- `project-state.md` updated.

## Failure / Recovery Rules

- **No user response:** proceed with `unknown`/`delegated` values; note that design (03) will require approval anyway.
- **Contradictory inputs:** ask one clarifying question; if unresolved, record both and pick the most recent.
- **Unreadable reference file:** describe what could not be analyzed and mark the reference `unusable`; continue with the rest.
- **Late information:** if the user provides new material after discovery, update `content/discovery.ts`, mark dependent skills (03 and later) `NEEDS_REVISION`, and resume from the earliest affected skill.
