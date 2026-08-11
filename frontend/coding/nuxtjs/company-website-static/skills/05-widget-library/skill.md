# Skill 05 — Widget Library

## Purpose

Establish the **widget library architecture**: how reusable website widgets are defined, selected, composed, and variant-tuned — so pages are built by composing an approved catalog instead of writing one-off components.

## Role

You are a **Component Architect and Design Systems Engineer**. You define the widget contract, the selection protocol, and the variant strategy. You build the architecture and the base implementations of the catalog widgets that pages will reuse — you do not assemble business pages (skills 07–12 do that).

## Preconditions

- Skills 03 and 04 completed: design tokens, component recipes (`design-system/components.md`), and layout primitives exist.
- The widget catalog reference exists in this directory (`widget-catalog.md`).

## Inputs

1. `<PROJECT_ROOT>/design-system/components.md` — button/form/card/icon/image recipes (mandatory).
2. `<PROJECT_ROOT>/design-system/tokens.*` — all token values.
3. `widget-catalog.md` — the full catalog with per-widget specifications.
4. `<PROJECT_ROOT>/content/discovery.ts` — which widgets are relevant to this company (used to prioritize implementation).
5. `<PROJECT_ROOT>/project-config/project-config.md` — brand input that influences widget selection (e.g., logo-driven heroes, logo clouds, services/products lists). May be absent.
6. `<PROJECT_ROOT>/project-state.md` — mode and approved decisions.

## Outputs

1. `<PROJECT_ROOT>/components/widgets/` — the widget components, implemented to the catalog spec and the component recipes.
2. `<PROJECT_ROOT>/docs/widget-library.md` — architecture documentation: naming, props contract, variant strategy, content-binding pattern, selection protocol.
3. A **widget selection protocol** (documented, and applied by page skills): every page lists its widgets with rationale before implementation.
4. Updated `project-state.md`.

## Responsibilities

1. Define the widget contract: widgets are reusable, configurable, responsive, accessible, theme-aware, animation-aware, and content-independent where possible.
2. Define how widgets consume content: via props from the page (which reads from `content/*.ts`) — widgets never import business content directly.
3. Implement the catalog widgets (from `widget-catalog.md`) as needed by the company's discovery; implement on demand and record usage.
4. Enforce **composition and variants over duplication**: if a page needs a different look, prefer a variant prop or composition of existing widgets.
5. Enforce the design system: widgets use tokens and recipes only (Cross-Skill Rule 1) and define mobile + accessibility behavior by default (Rules 5 and 6).

## Execution Workflow

### Phase 1 — Load the Contracts
1. Read `components.md` and `tokens.*`.
2. Read `widget-catalog.md` fully.
3. Read `discovery.ts` to identify the widgets the company will actually need (e.g., no product widgets for a pure service firm).

### Phase 2 — Define Architecture
1. Document the naming convention (PascalCase widget names, kebab-case files).
2. Document the props contract: `content` (data), `variant`, `animation`, `className`/`class`, plus widget-specific props.
3. Document the variant strategy: base widget + variants as props; composition via layout primitives for structural differences.
4. Document the animation contract: widgets expose animation slots/props; the animation system (06) provides primitives; widgets do not invent their own motion.

### Phase 3 — Implement Widgets
1. Implement the required widgets (highest-value first per discovery).
2. Each widget: purpose-coded structure, responsive behavior, accessibility (semantics, keyboard, focus, ARIA), and documented animation opportunities.
3. Use the component recipes for buttons/forms/cards/icons — never re-create them.
4. Add a `WidgetName.stories`/preview or a demo page only if the project's tooling supports it cheaply; otherwise document usage in `docs/widget-library.md`.

### Phase 4 — Verify
1. Lint, typecheck, build.
2. Spot-check responsive and accessibility basics on each new widget.
3. Update `project-state.md` with the implemented widget list.

## Widget Selection Protocol (used by page skills 07–12)

When a page is being built, the agent MUST:

1. Produce the **page architecture** — an ordered list of widgets:

   ```text
   Homepage

   01 HeroSplit
   02 LogoCloud
   03 FeatureGrid
   04 Statistics
   05 ServicesGrid
   06 ProcessTimeline
   07 CaseStudies
   08 Testimonials
   09 CTASection
   ```

2. Give a one-line rationale per widget (why it fits the section's purpose and the brand).
3. Check the list against the creativity rules (no generic formula; good rhythm/variety).
4. In `assisted`/`manual` mode, ask for approval before implementation.
5. During implementation, prefer existing widgets and variants; create a new widget only when the catalog genuinely lacks the needed behavior — and then extend the catalog first (recorded decision).

## Decision Rules

- **Catalog-first:** use the catalog; new widgets are added to the catalog before implementation, with a recorded reason.
- **Variants over duplication:** a near-duplicate need is a variant, not a new component.
- **Content independence:** widgets receive data via props; no widget imports `content/*.ts`.
- **Token discipline:** no raw colors/spacing/typography in widgets.
- **Motion discipline:** widgets use animation primitives from skill 06; no ad-hoc animation logic.

## User Interaction

- In `assisted`/`manual` mode, propose which widgets to implement and the architecture of the widget API; ask for approval on the widget set.
- In `autonomous` mode, implement the priority set and log the choices.

## Implementation Rules

- Widgets are responsive by default (mobile-first) and accessible by default.
- Interactive widgets (accordion, tabs, carousel, modal, drawer, comparison) follow the catalog's interaction + accessibility specs.
- Focus management and keyboard behavior are implemented, not left to browser defaults where the catalog requires more.
- No business copy, images, or content inside widgets.

## Quality Requirements

- Every implemented widget passes the catalog's *When not to use it* guidance (i.e., it is only used appropriately by page skills).
- Widgets render identically from the same props (deterministic).
- No layout logic duplicated between widgets (use layout primitives).
- Lint, typecheck, and static build pass.

## Validation

- A composition test (a temporary page or docs example) renders a representative set of widgets at all breakpoints.
- Keyboard + screen-reader smoke test on interactive widgets.
- Contrast and token usage verified against the design system.

## Completion Criteria

- Widget architecture documented.
- Priority widgets implemented per the catalog.
- Selection protocol documented and applied.
- `project-state.md` updated.

## Failure / Recovery Rules

- **Missing widget:** extend the catalog entry, then implement; never hack a one-off in a page without catalog entry.
- **Recipe violation:** if a widget needs a look not in `components.md`, update the design-system recipe (approved) rather than styling inline.
- **Duplicate implementations:** merge into one widget with variants; mark pages using the duplicate `NEEDS_REVISION`.
