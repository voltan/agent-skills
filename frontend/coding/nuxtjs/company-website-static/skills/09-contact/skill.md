# Skill 09 — Contact

## Purpose

Design and implement the **Contact page** as a **fully static** page. The website has no backend: the page presents contact information and offers contact methods that work without a server. No backend contact API is assumed or introduced.

## Role

You are a **UX/UI Architect** focused on conversion and trust. You build a contact experience that works entirely from static content.

## Preconditions

- Skills 03–06 completed.
- `content/site.ts` exists with contact information placeholders (email, phone, address, socials).
- Mode known.

## Inputs

1. Design system (`design-direction.md`, `tokens.*`, `components.md`).
2. `content/discovery.ts` — contact preferences, required contact methods.
3. `content/site.ts` — contact data placeholders.
4. `widget-catalog.md`, `creativity-rules.md`.
5. `project-config/project-config.md` — brand input (contact data, content notes).
6. `project-state.md`.

## Outputs

1. `<PROJECT_ROOT>/pages/contact.vue` — the Contact page.
2. Any new catalog widgets required (recorded).
3. Updated `project-state.md`.

## Responsibilities

1. Present static contact information: email, phone, address, hours (placeholders).
2. Provide working static contact methods: `mailto:` email links, `tel:` phone links, address + map link (e.g., Google/OpenStreetMap URL — a link, not an embedded API key dependency), social links.
3. Present a **static form presentation** only if desired: the form is non-submitting (it explains how to reach the company, or uses an external form provider **only if explicitly requested by the user**).
4. Never introduce backend dependencies automatically.

## Contact Approaches (in priority order)

1. **Static contact information** — always included: email, phone, address, hours.
2. **Email links** (`mailto:`) and **phone links** (`tel:`) — direct actions.
3. **Address + map link** — a plain link to an external map (no embedded API).
4. **Social links** — to configured profiles (placeholders until real).
5. **Static form presentation** — a styled, non-submitting form that displays contact instructions (or `mailto` composition); clearly non-functional without a backend.
6. **External form provider** — only if the user explicitly requests it (e.g., a form service); never auto-added.

## Execution Workflow

### Phase 1 — Analyze
1. Read discovery + `content/site.ts` for the required contact methods.
2. Read `project-config.md` content notes — contact data provided there (address, phone, email, social links, map link) is authoritative and must end up on the page.
3. Decide the page architecture (typically: hero, contact channels grid/cards, map link block, optional form presentation, CTA).

### Phase 2 — Propose Architecture
1. Widget selection with rationale (e.g., HeroMinimal, FeatureGrid of contact channels, SplitCTA with contact card).
2. Confirm the contact approach (static only, unless user explicitly requested a provider).

### Phase 3 — Approve
1. Per mode: present and get approval (assisted/manual) or record (autonomous).

### Phase 4 — Implement
1. Build `pages/contact.vue` with layout primitives + widgets.
2. All contact values come from `content/site.ts` (placeholders marked).
3. Apply animations; responsive + accessibility basics; self-QA.

### Phase 5 — Report
1. Update `project-state.md`.

## Decision Rules

- **Static-only (Cross-Skill Rule 8):** no contact API, no email-sending service, no server endpoints, no database of submissions.
- **No auto-integrations:** an external form provider is added only on explicit user request.
- **No invented contact details:** placeholders (`[Placeholder: email]`) are flagged for skill 13; the page must still render sensibly with placeholders.
- **Trust over friction:** never use a form as the only contact method on a static site.

## User Interaction

- Ask which contact methods the company actually has (email/phone/address/socials).
- If the user asks for a working form, explain the static constraint and offer the options above; implement an external provider only with explicit approval.
- Present the architecture for approval per mode.

## Implementation Rules

- `tel:`/`mailto:` links have `href` values from content; labels are human-readable.
- Address rendered as text + optional map link (new tab, `rel="noopener"`).
- Form presentation (if included): labeled fields, accessible inputs, visible note that it's a static presentation, and a fallback instruction (e.g., "Email us at …").
- No tracking scripts added.

## Quality Requirements

- All contact methods work statically (links open the right action).
- No backend dependency introduced anywhere.
- Placeholder contact data clearly marked.
- Page matches the design system.

## Validation

- Static build passes; links (`mailto:`, `tel:`, social) are correct.
- No network calls to backend endpoints on the page.
- Form presentation (if any) is accessible and honest about being static.
- Responsive smoke test passes.

## Completion Criteria

- Contact page implemented with static methods only.
- Content bound from `content/site.ts`.
- `project-state.md` updated.

## Failure / Recovery Rules

- **Missing contact data:** render placeholders; list them for skill 13; never invent an email/phone.
- **User insists on a working form:** explain constraints; only add an external provider with explicit approval (recorded).
- **Link errors:** validate all generated links before completion.
