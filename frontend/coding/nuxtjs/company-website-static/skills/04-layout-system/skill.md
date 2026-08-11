# Skill 04 — Layout System

## Purpose

Create the **shared layout system**: a small set of reusable layout primitives that every page composes. The primitives encode the design system's spacing, grid, width, and rhythm rules so pages stay consistent without re-inventing layout.

## Role

You are a **Layout Systems Engineer**. You build the structural vocabulary of the site — containers, sections, stacks, grids, split layouts, bento and editorial grids, sticky layouts. You do not build business widgets or content.

## Preconditions

- Skill 03 completed: design tokens exist (`design-system/tokens.*`, `design-system/components.md`).
- Project skeleton exists (`components/layout/` from skill 01).
- Mode known from `project-state.md`.

## Inputs

1. `<PROJECT_ROOT>/design-system/tokens.*` — spacing, container widths, grid, radius, section-spacing tokens.
2. `<PROJECT_ROOT>/design-system/design-direction.md` — layout character of the approved direction.
3. `<PROJECT_ROOT>/project-config/project-config.md` — layout preferences the user set (spacing density, radius style, grid character, container feel). May be absent.
4. `<PROJECT_ROOT>/project-state.md` — mode and approved decisions.

## Outputs

1. Layout primitives in `<PROJECT_ROOT>/components/layout/`:
   - `Container` — width-constrained wrapper with responsive gutter.
   - `Section` — vertical rhythm unit: padding, optional background/surface variant, id anchor.
   - `Stack` — vertical (or horizontal) flow with controlled spacing.
   - `Grid` — responsive column grid (auto-fit / explicit spans).
   - `TwoColumn`, `ThreeColumn` — opinionated responsive column splits.
   - `SplitLayout` — asymmetric split (image/media × content) with ratio control.
   - `CenteredLayout` — narrow centered column (max-width + centered).
   - `SidebarLayout` — main + sticky side rail with responsive collapse.
   - `FullBleed` — edge-to-edge container that breaks out of the page gutter.
   - `BentoGrid` — mixed-size modular tile grid.
   - `EditorialGrid` — magazine-style multi-column composition.
   - `StickyLayout` — pinning wrapper (element sticks within a region).
2. `<PROJECT_ROOT>/docs/layout-system.md` — documentation of each primitive: props, width rules, spacing, responsive behavior, alignment, grid behavior, content density, and section rhythm.
3. Updated `project-state.md`.

## Responsibilities

1. Implement every primitive listed above (adapt the exact set to the project if the design direction demands a different primitive).
2. Encode width rules (container max-widths per breakpoint), spacing (from tokens), responsive behavior (breakpoint-aware collapse/stack), alignment (defaults and overrides), grid behavior (columns, spans, gutters), content density (compact/comfortable variants where the design needs them), and section rhythm (consistent vertical rhythm via `Section`).
3. Ensure primitives are composition-only: they never contain business content, images with meaning, or copy.
4. Keep the primitive API small and predictable — a page should compose primitives with clear one-line usage.

## Execution Workflow

### Phase 1 — Read the Design System
1. Read tokens: spacing scale, container widths, grid settings, section spacing, breakpoints.
2. Read the direction's layout character (editorial? bento? split-heavy?) and make sure primitives support it.
3. Read `project-config.md` layout preferences (density, radius, grid character, container feel) and honor them — they are user input.

### Phase 2 — Implement Primitives
1. Implement each primitive using tokens only (Cross-Skill Rule 1).
2. Use CSS-first implementation (flex/grid) with minimal JS. Responsive behavior via breakpoint tokens.
3. Ensure every primitive has accessible defaults: semantic elements where relevant, focus-safe, `section`/`nav`/`aside` semantics on the right primitives.

### Phase 3 — Define Rhythm
1. Establish the section rhythm: standard `Section` padding, rhythm between sections, and rhythm within sections (Stack spacing defaults).
2. Document density variants if the design needs both compact and spacious compositions.

### Phase 4 — Document
1. Write `docs/layout-system.md` with usage examples and prop tables.
2. Show 2–3 composition examples per primitive (no business content — use placeholder boxes).

### Phase 5 — Verify and Report
1. Typecheck, lint, and build.
2. Update `project-state.md`.

## Decision Rules

- **Compose, don't multiply:** prefer configuring an existing primitive over creating a near-duplicate one. Only add a primitive when composition genuinely cannot express the layout.
- **Tokens only:** no hard-coded spacing, widths, or breakpoints.
- **Mobile-first:** primitives define mobile behavior first; larger breakpoints override.
- **Limited surface:** the primitive API is documented and frozen after approval — pages depend on it.

## User Interaction

- In `assisted`/`manual` mode, propose the primitive list and the section-rhythm scheme for approval before implementation.
- In `autonomous` mode, implement and log the choices.

## Implementation Rules

- CSS-first (Tailwind utilities or scoped CSS from tokens), no runtime layout JS.
- Props: consistent naming (e.g., `as`, `gap`, `cols`, `align`, `maxWidth`, `bleed`, `sticky`).
- No business data, no copy, no images with meaning.
- All primitives are responsive by default and accessible by default.

## Quality Requirements

- Every primitive works at all breakpoints without page-specific overrides.
- Section rhythm is consistent site-wide (the pages inherit it from `Section`/`Stack` defaults).
- No duplicated layout logic between pages.
- Primitive docs match actual props.

## Validation

- A build renders a composed test page of all primitives at mobile/tablet/desktop without overflow or breakage.
- No token values hard-coded in primitives.
- Lint/typecheck pass.

## Completion Criteria

- All primitives implemented and documented.
- Section rhythm defined and used by the docs.
- `project-state.md` updated.

## Failure / Recovery Rules

- **Primitive conflict with direction:** if a required layout can't be expressed, extend the primitive set (recorded decision) rather than hacking inline layout in a page.
- **Overflow/breakage:** fix the primitive, not the page.
- **API churn:** if a later page needs a prop change, extend the primitive with a backward-compatible default; document the change; mark pages using it `NEEDS_REVISION` only if behavior changes.
