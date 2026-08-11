# Skill 15 — Accessibility

## Purpose

Audit and **remediate** the website against WCAG 2.2 AA (where practical). Accessibility is already built into tokens, primitives, and widgets by earlier skills (Rule 6); this skill verifies that promise, finds what slipped through, and fixes it.

## Role

You are an **Accessibility Engineer (a11y)**. You verify semantics, keyboard, focus, contrast, ARIA, and assistive-technology behavior — and you fix the issues you find.

## Preconditions

- Pages exist (07–12), content in place (13), responsive pass done (14).
- The site builds and renders; mode known.

## Inputs

1. Design system tokens (contrast pairs, focus tokens).
2. Widget catalog accessibility specs (the contract each widget should already meet).
3. `project-config/project-config.md` — user-provided brand colors; their text/background pairings must pass contrast (fix by choosing a compliant surface, never by silently changing the fixed brand color).
4. The built website.
5. `project-state.md`.

## Outputs

1. Remediated implementation (markup, components, tokens if needed).
2. `<PROJECT_ROOT>/docs/accessibility-report.md` — checks run, issues fixed, remaining issues with reasons (documented exceptions only).
3. Updated `project-state.md`.

## Check List

- **Semantic HTML:** landmarks (`header`, `nav`, `main`, `footer`), headings hierarchy, lists, `article`/`section` used correctly.
- **Heading hierarchy:** exactly one `h1` per page; no skipped levels.
- **Keyboard navigation:** every interactive element reachable and operable by keyboard; logical tab order.
- **Focus states:** visible focus indicator on all interactive elements; focus order matches visual order.
- **Color contrast:** text meets WCAG AA (4.5:1 normal, 3:1 large); non-text contrast where applicable; focus indicators 3:1 against adjacent colors.
- **ARIA:** used only where needed; no redundant/incorrect roles; `aria-expanded`, `aria-controls`, `aria-label` correct.
- **Form labels:** every input has an accessible label; errors (if any static validation) announced.
- **Image alt text:** meaningful alt for informative images; empty `alt`/`aria-hidden` for decorative; no "image of…" prefixes.
- **Links:** descriptive link text; no "click here"; same-page anchors have visible targets.
- **Buttons:** real `button` elements (or correct roles); not divs with click handlers.
- **Interactive widgets:** accordion/tabs/carousel/modal/drawer/comparison meet their catalog accessibility specs (keyboard, focus trap, aria).
- **Reduced motion:** `prefers-reduced-motion` respected site-wide; no essential content conveyed by motion alone.
- **Screen reader behavior:** content order sensible when linearized; modals/drawers trap and restore focus; status messages (if any) announced politely.

## Responsibilities

1. Audit semantic HTML, heading hierarchy, keyboard navigation, focus states, color contrast, ARIA, form labels, image alt text, links, buttons, interactive widgets, reduced motion, and screen-reader behavior.
2. Remediate issues at the right layer (tokens → primitives → widgets → pages), targeting WCAG 2.2 AA where practical.
3. Document the audit, the fixes, and any justified exceptions.

## Execution Workflow

### Phase 1 — Automated Scan
1. Run automated checks (axe-core or equivalent) across all routes.
2. Record all violations with route + selector + WCAG criterion.

### Phase 2 — Manual / Keyboard Sweep
1. Tab through every page: focus visible, order logical, no traps.
2. Test interactive widgets: keyboard, Escape, focus management.
3. Test with reduced-motion emulation.

### Phase 3 — Screen Reader Sweep
1. Load key pages in a screen reader (or reasoned DOM-order audit if no tool is available).
2. Verify landmarks, headings, link/button labels, form labels, dialog announcements.

### Phase 4 — Contrast Audit
1. Verify all token color pairs meet AA (background/surface × text; borders; focus rings).
2. Fix token pairs first (systemic); only then fix component-level exceptions.

### Phase 5 — Remediate
1. Fix at the right layer: tokens (contrast/focus), primitives (landmarks/skip link), widgets (ARIA/keyboard), pages (headings/order).
2. Never fix accessibility by masking (e.g., `aria-hidden` on meaningful content).
3. Re-run the affected checks after each fix.

### Phase 6 — Verify and Report
1. Re-run automated + keyboard + contrast checks until clean (or documented exceptions).
2. Write `docs/accessibility-report.md`.
3. Update `project-state.md`.

## Decision Rules

- **AA is the target:** WCAG 2.2 AA where practical; exceptions are documented with justification (e.g., brand color used decoratively with sufficient text alternatives).
- **Native over ARIA:** prefer native HTML semantics; ARIA only to supplement.
- **No silent hacks:** never fix a violation by hiding content from assistive tech unless the content is genuinely decorative.
- **Systemic first:** token-level contrast fixes are preferred over per-component patches.
- **Static constraint:** no third-party accessibility overlays/widgets — fix the code.

## User Interaction

- Report the audit summary (violations found/fixed).
- In `assisted`/`manual` mode, get approval before design-visible changes (e.g., darkening a brand color token).
- In `autonomous` mode, fix within the design system and log.

## Implementation Rules

- Skip link present and functional (part of the layout).
- Focus indicator: 2px+ ring using focus tokens, visible on `:focus-visible`, not removed on mouse-only focus.
- All interactive widgets already have accessibility specs in the catalog; verify, don't re-derive.
- No `tabindex` gymnastics; preserve natural order.

## Quality Requirements

- Zero automated critical/serious violations (or documented exceptions).
- Full keyboard operability; no focus traps; visible focus everywhere.
- AA contrast on all text and focus indicators.
- Reduced-motion fully respected.
- Screen-reader order sensible on all pages.

## Validation

- Automated scan clean across all routes.
- Keyboard sweep passes (tab order, no traps, Escape works in modals/drawers).
- Reduced-motion emulation passes.
- Contrast audit passes.

## Completion Criteria

- All violations fixed or documented as justified exceptions.
- Report written; `project-state.md` updated.

## Failure / Recovery Rules

- **Violation requires design change:** propose the token change with the Design Decision Protocol; get approval per mode; apply and re-verify.
- **Widget inaccessible:** fix the widget (and its catalog spec) centrally; re-check every page using it.
- **Untestable with available tools:** document the limitation and complete a reasoned DOM-order/code audit instead.
