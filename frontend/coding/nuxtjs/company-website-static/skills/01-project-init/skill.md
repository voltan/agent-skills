# Skill 01 — Project Init

## Purpose

Initialize a clean, production-quality **Nuxt.js static corporate website** project inside `<PROJECT_ROOT>`: correct tooling, architecture, conventions, and empty (non-business) foundations that every later skill builds upon.

## Role

You are a **Principal Nuxt.js Architect and Tooling Engineer**. You establish the foundation only — you do **not** create business pages, business content, or business-specific design.

## Preconditions

- Skill 00 dispatched this skill; mode is known (default `assisted`).
- `<PROJECT_ROOT>` exists and is empty (or contains only `project-state.md`).
- No previous project files conflict with the new foundation.

## Inputs

1. `<PROJECT_ROOT>/project-state.md` — active mode and any approved decisions.
2. Installed environment: Node version, available package managers. **Never hard-code framework versions** — verify the current stable Nuxt version and its requirements at runtime against the official Nuxt documentation.

## Outputs

1. A complete Nuxt project skeleton at `<PROJECT_ROOT>`: `nuxt.config.ts`, `package.json`, TypeScript config, Tailwind config/plugin, ESLint, Prettier, `.gitignore`, and directory structure.
2. The **content sources** skeleton: `content/site.ts`, `content/homepage.ts`, `content/about.ts`, `content/services.ts`, `content/products.ts`, `content/legal.ts` — each exporting typed placeholder records (see skill 13 conventions).
3. The **design-system** skeleton directory: `design-system/` (tokens to be filled by skill 03) and `design-system/README.md` explaining where tokens and the design direction will live.
4. Architecture documentation: `docs/architecture.md` describing the component, layout, asset, content, and route organization.
5. Updated `project-state.md`: status `COMPLETED`, list of created artifacts.

## Responsibilities

1. Scaffold the Nuxt project (static generation) with TypeScript enabled.
2. Configure Tailwind CSS wired to design tokens (tokens themselves come from skill 03).
3. Set up ESLint + Prettier with strict, consistent rules.
4. Add Nuxt Image where appropriate for the installed Nuxt version.
5. Add SEO tooling compatible with static output (e.g., Nuxt SEO modules or manual `useSeoMeta`/`useHead` conventions).
6. Configure static generation (`ssr: false` + prerender / `nuxt generate` equivalent for the installed version).
7. Establish the component architecture, layout architecture, asset organization, content organization, route organization, and development conventions.
8. Create typed placeholder content sources so later skills (07–13) have a stable content contract.

## Execution Workflow

### Phase 1 — Verify Environment
1. Confirm Node and package manager availability.
2. Confirm the current stable Nuxt version and its static-generation mode; record the chosen version in `project-state.md`.

### Phase 2 — Scaffold
1. Initialize the Nuxt project with TypeScript.
2. Install and configure: Tailwind CSS, ESLint, Prettier, Nuxt Image (if appropriate), and SEO tooling.
3. Verify the project generates a static site (`nuxt generate` / prerender succeeds) with an empty page.

### Phase 3 — Establish Architecture
1. Create the directory skeleton with README markers (empty directories are documented, not committed empty):
   - `components/layout/` — layout primitives (skill 04)
   - `components/widgets/` — reusable widgets (skill 05)
   - `components/anim/` — animation primitives (skill 06)
   - `layouts/` — page layouts
   - `pages/` — routes (empty; no business pages)
   - `assets/` — images, fonts, global CSS
   - `public/` — static assets (favicon, robots placeholder)
   - `content/` — structured content sources
   - `design-system/` — tokens and design direction (skill 03)
2. Write `docs/architecture.md` documenting every decision below.

### Phase 4 — Configure Tooling
1. TypeScript strict mode; no `any` without justification.
2. Tailwind theme structured to consume design tokens (colors, typography, spacing, radius, shadows as token variables).
3. ESLint: Vue + TypeScript recommended rulesets; accessibility and complexity rules enabled.
4. Prettier: single consistent style; integrated with ESLint.
5. Git: `.gitignore` for `node_modules`, `.nuxt`, `.output`, `dist`, and editor files.

### Phase 5 — Create Content Sources
1. Create `content/*.ts` with typed interfaces (e.g., `Site`, `HomepageContent`, `Service`, `Product`, `LegalPage`).
2. Fill them with clearly marked placeholder values using the convention `[Placeholder: ...]` and Lorem ipsum where paragraphs are needed.
3. These sources are the single source of truth for all page content (skills 07–12 read from them; skill 13 replaces them).

### Phase 6 — Verify Static Build
1. Run the static build and a local preview; confirm the shell renders.
2. Confirm no server-side dependency was introduced.

### Phase 7 — Report
1. Update `project-state.md` with the created structure, versions used, and status `COMPLETED`.

## Decision Rules

- **Version strategy:** prefer the current stable Nuxt major version; verify at runtime; document the choice and its migration notes. Never pin an obsolete version without a recorded reason.
- **Styling:** Tailwind CSS as the default; design tokens are the single source of styling values.
- **Static-only:** any module or config that requires a server at runtime is rejected.
- **No business content:** do not invent company name, services, products, or copy at this stage.
- **Minimal dependencies:** add only tooling that the later skills demonstrably need; avoid speculative packages.

## User Interaction

- In `assisted` mode, propose: Nuxt major version choice, styling approach, and SEO tooling; ask for approval if the user has preferences.
- In `manual` mode, ask before each significant tooling decision.
- In `autonomous` mode, choose and log the decisions with rationale.

## Implementation Rules

- All code is TypeScript with strict typing; exported interfaces for every content model.
- Design tokens are referenced but not yet defined (skill 03 fills them); use CSS variables / Tailwind theme placeholders only.
- No business-specific pages, components, copy, images, logos, or design.
- Everything is documented: architecture, commands (dev, generate, preview, lint, format) in README/`package.json` scripts.
- Directory names follow kebab-case; component names follow PascalCase; content files follow kebab-case.

## Quality Requirements

- `nuxt generate` succeeds from a clean state.
- ESLint and Prettier pass on the scaffolded code.
- TypeScript strict compilation passes with zero errors.
- The scaffold has no dead dependencies and no business content.

## Validation

- Static build passes.
- Lint/format/typecheck pass.
- `docs/architecture.md` exists and matches the actual structure.
- `content/*.ts` exists, is typed, and uses placeholder conventions.

## Completion Criteria

- Empty Nuxt static project runs and generates.
- Architecture, tooling, and content skeletons exist and are documented.
- `project-state.md` updated; no business content present.

## Failure / Recovery Rules

- **Scaffold failure:** re-run the official Nuxt init flow for the chosen version; if a dependency conflicts, pin documented compatible versions and record the change.
- **Static build breaks:** remove the offending module/configuration; the project must always generate statically.
- **Existing files conflict:** if `<PROJECT_ROOT>` already contains files (e.g., a half-started project), reconcile — never overwrite approved work; mark affected areas `NEEDS_REVISION`.
- **User changes tooling preference:** reconfigure cleanly, record the decision, and mark dependent skills `NEEDS_REVISION`.
