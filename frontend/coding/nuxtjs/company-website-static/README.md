# Nuxt.js Static Corporate Website Builder — AI Skill System

A reusable, sequential **AI Skill System** for designing, implementing, and maintaining **modern, creative, responsive, accessible, SEO-friendly, high-performance, fully static corporate websites with Nuxt.js**.

This is **not** a website and **not** website code. It is an instruction system: each `skill.md` in the skill folders is a complete, self-contained methodology that an AI coding agent loads and executes inside a website project to generate and maintain a complete corporate website over time.

---

## Quick Start

### 1. Create a new website project

Create an empty directory for the website:

```text
my-company-website/
```

### 2. Copy the reusable skills

Copy the skill folders from this repository into the website project under `skills/`:

```text
skills/
├── 00-orchestrator/
├── 01-project-init/
├── 02-discovery/
├── 03-design-system/
├── ...
└── 18-visual-qa/
```

### 3. Copy the runner

Copy `run-website-builder.md` (the runner) into the website project root.

### 4. Copy and fill the project configuration

Copy the `project-config/` folder into the website project root, rename `project-config.example.md` to `project-config.md`, and fill in the brand settings — colors, design direction, logo files (into `project-config/brand/`), and design sample images (into `project-config/references/`). See [Project Configuration](#project-configuration-project-config).

### 5. Start the AI coding agent

Open the AI coding agent from the website project root (`my-company-website/`).

### 6. Execute the workflow

Tell the agent:

> Execute `run-website-builder.md` and continue the website generation workflow from the current project state.

The agent will determine which Skill should run next — **you do not need to tell it**.

---

## Modifying an Existing Website

Start the AI agent from the website project root and tell it what you want to change:

> Read `run-website-builder.md` and the `.website-builder/` project history. Change the homepage hero to a more premium visual style while preserving the existing design system.

The agent should inspect the project history and the current implementation, then perform **only the required changes** — never rerun the whole generation workflow for a small change.

---

## Three Concepts

The system has three strictly separate concepts:

### 1. Reusable Skill System (`skills/`)

Reusable instructions that describe **HOW an AI agent should build and maintain** a static Nuxt.js corporate website:

```text
skills/
├── 00-orchestrator/
├── 01-project-init/
├── 02-discovery/
├── 03-design-system/
├── ...
└── 18-visual-qa/
```

These skills are reusable across multiple websites. They are copied into each website project and normally remain **unchanged** during website development.

### 2. Project Configuration (`project-config/`)

The **brand input for one particular website**: colors, design direction, layout preferences, logo/brand asset files, design reference images, and content notes. This is where you put the color scheme (رنگبندی), the overall design (طرح کلی), design sample images, and logos for each project.

```text
project-config/
├── README.md                  ← how to use this folder
├── project-config.md          ← filled from project-config.example.md
├── brand/                     ← logo / favicon / brand asset files
└── references/                ← design sample images
```

Copy the folder into each website, then fill `project-config.md` and drop the assets in. See [Project Configuration](#project-configuration-project-config) below. This folder is **input**, not history: approvals and changes are still recorded in `.website-builder/`.

### 3. Website-Specific State (`.website-builder/`)

The memory and history of **one particular website**:

```text
.website-builder/
├── state.md
├── decisions.md
├── changelog.md
├── design-history.md
└── qa-history.md
```

This directory belongs only to that website and **must not be shared between unrelated websites**.

| Concept | Role |
| :--- | :--- |
| `skills/` | **HOW to work** — reusable instructions |
| `project-config/` | **WHAT the site should look like** — brand input: colors, design direction, logos, reference images, content notes |
| `.website-builder/` | **WHAT happened** — what was decided, approved, built, and QA'd for this site |

---

## This Repository (the Skill System Source)

The reusable skill system lives in this directory. Reusable skills are kept in **numbered folders inside `skills/`** (each website receives its own copy of `skills/`), and the per-project brand configuration template lives in `project-config/`:

```text
company-website-static/
├── README.md                  ← this document
├── run-website-builder.md     ← the runner: execution entry point for AI agents
├── master-prompt.md           ← the original master prompt that defined the system
├── skills/                    ← reusable skills (copied into each website)
│   ├── 00-orchestrator/       ← skill: orchestration, state, execution modes
│   ├── 01-project-init/       ← skill: Nuxt static foundation & tooling
│   ├── 02-discovery/          ← skill: company/brand/visual-reference discovery
│   ├── 03-design-system/      ← skill + creativity-rules.md
│   ├── 04-layout-system/      ← skill: layout primitives
│   ├── 05-widget-library/     ← skill + widget-catalog.md
│   ├── 06-animation-system/   ← skill: motion patterns & rules
│   ├── 07-homepage/           ← skill: homepage
│   ├── 08-about/              ← skill: about page
│   ├── 09-contact/            ← skill: static contact page
│   ├── 10-services/           ← skill + service-detail.md
│   ├── 11-products/           ← skill + product-detail.md
│   ├── 12-legal/              ← skill: legal pages
│   ├── 13-content-replacement/← skill: real content replaces placeholders
│   ├── 14-responsive/         ← skill: responsive QA + fixes
│   ├── 15-accessibility/      ← skill: WCAG 2.2 AA audit + remediation
│   ├── 16-seo/                ← skill: SEO implementation + audit
│   ├── 17-performance/        ← skill: performance audit + optimization
│   └── 18-visual-qa/          ← skill: screenshot-based visual QA
└── project-config/            ← per-project brand configuration template
    ├── README.md              ← how to use this folder
    ├── project-config.example.md ← the template to copy and fill per website
    ├── brand/                 ← drop logo / favicon / brand asset files here
    └── references/            ← drop design sample images here
```

To create a website, copy the `skills/` folder and the `project-config/` folder (and the runner) into the website project as described in **Quick Start**.

---

## The Website Model

Every website is an independent project. Example workspace:

```text
websites/
│
├── company-a/
│   ├── skills/            ← copy of the reusable Skill System
│   ├── project-config/    ← this website's brand input (colors, direction, logos, references)
│   ├── .website-builder/  ← this website's memory (state, decisions, history)
│   ├── app/               ← website implementation
│   ├── pages/
│   ├── components/
│   ├── content/           ← structured content sources
│   ├── design-system/     ← approved tokens + design direction
│   └── ...
│
└── company-b/
    ├── skills/
    ├── project-config/
    ├── .website-builder/
    ├── app/
    ├── pages/
    ├── components/
    └── ...
```

Rules:

- `skills/` is **copied** from the reusable Skill System and normally stays unchanged during website development.
- `project-config/` is **copied** per website and filled with that website's brand settings — colors, design direction, logos in `brand/`, design samples in `references/`. It is input, not history; changes still get approved and recorded in `.website-builder/`.
- `.website-builder/` belongs **only to that website** — it holds the site's history and must not be shared with other websites.
- The website implementation (code, content, design tokens) belongs **only to that website**.
- Skills should normally remain unchanged; website-specific decisions go into `.website-builder/`, never into skill files.

---

## Project Configuration (`project-config/`)

The **project configuration** is where each website's brand input lives: the color scheme, the overall design direction, layout preferences, logo files, design sample images, and content notes.

```text
<website>/project-config/
├── README.md                  ← how to use this folder
├── project-config.md          ← the filled configuration (copy of project-config.example.md)
├── brand/                     ← logo / favicon / brand asset files (logo.svg, logo-dark.svg, …)
└── references/                ← design sample images (hero-style-01.jpg, mockups, …)
```

### How to fill it

1. Copy `project-config/` into the website project root.
2. Rename `project-config.example.md` → `project-config.md`.
3. Fill in what you know: company identity, colors (with hex values), typography, design direction (or leave it to the agent to propose), content notes.
4. Drop assets in: logos → `brand/`, design samples → `references/`.
5. Reference the asset files by relative path in `project-config.md` (e.g. `brand/logo.svg`, `references/hero-style-01.jpg`).

Anything left empty is proposed by the agent during Discovery (02) and Design System (03) and approved by you before implementation.

### How the agents consume it

- **01 Project Init** verifies `project-config/` exists and scaffolds the template if missing.
- **02 Discovery** reads `project-config.md` first and never re-asks for anything already provided.
- **03 Design System** uses the configured colors/typography/direction as the base and honors them like user decisions.
- **07–12 Pages** use the configured logo and brand assets; **13 Content** uses the content notes; **16 SEO** uses the logo; **18 Visual QA** compares the result against the reference images.

> **Rule:** configuration values are user input — treat them like approved decisions and never silently override them (Cross-Skill Rule 2).

---

## The Runner (`run-website-builder.md`)

`run-website-builder.md` (also referred to as `RUN-WEBSITE-BUILDER.md`) is the **execution entry point** for the AI agent. It is copied into each website project and tells the agent to:

1. Inspect the current website (project root, filesystem, existing Nuxt project, pages, components, content, assets, Git state).
2. Inspect the local `skills/` directory (the authoritative source of instructions for this website).
3. Inspect `.website-builder/` (state, decisions, history) if it exists.
4. Determine the current workflow state.
5. Determine which Skill should run next.
6. Execute Skills in the correct order.
7. Ask the user only when important decisions require approval (Assisted Mode by default).
8. Save decisions and progress to `.website-builder/`.
9. Continue from the previous state after interruption — **never restart from Skill 00 blindly**.
10. Perform QA (responsive, accessibility, SEO, performance, visual).
11. Maintain the website over time (handle future change requests with minimal scope).

> **The user does not need to manually tell the agent which Skill to execute next.**

The agent determines the next Skill from:

```text
skills/
+
.website-builder/state.md
+
the actual project implementation
```

---

## Initial Website Creation (Detailed Workflow)

1. Create a new website directory.
2. Copy the reusable skills into `skills/`.
3. Copy `run-website-builder.md`.
4. Copy `project-config/` and fill `project-config.md` — colors, design direction, logo (into `brand/`), design references (into `references/`).
5. Start the AI coding agent from the website project root.
6. Ask the agent to execute `run-website-builder.md`.
6. The agent initializes the project (Skill 01).
7. The agent runs discovery (Skill 02).
8. The agent asks for design information when needed.
9. The agent analyzes visual references if provided.
10. The design system is created (Skill 03).
11. The website is implemented page by page (Skills 07–12).
12. Content is initially placeholder content.
13. Content is replaced later (Skill 13).
14. Responsive, accessibility, SEO, performance, and visual QA are performed (Skills 14–18).
15. The production build is verified.

Concrete example prompt:

```text
Execute run-website-builder.md and build this website from the current project state.
```

The exact wording can vary, but the agent must be instructed to **use the runner file as the workflow entry point**.

---

## Visual Reference Workflow

During the **Discovery (02)** and **Design System (03)** stages, the user can provide:

- Screenshots
- Images
- Existing website URLs
- Brand references
- Logos
- Color palettes
- Typography references
- Written descriptions

The agent analyzes each reference for:

```text
Layout, Composition, Typography, Colors, Spacing, Grid,
Components, Image treatment, Visual hierarchy, Animation language
```

The agent **derives design principles** (mood, structure, hierarchy, rhythm) from the references — it does **not** blindly copy another website, and never copies copyrighted text, logos, brand assets, or exact content. Extracted principles and reference pointers are recorded in the discovery result and `.website-builder/design-history.md`.

---

## Execution Modes

The default workflow is **Assisted Mode**:

- The AI makes normal implementation decisions automatically (variable names, file names, minor CSS values, standard Nuxt conventions — no questions).
- The AI asks the user for approval **only for significant decisions**, such as:
  - Design direction
  - Major color system
  - Typography direction
  - Homepage architecture
  - Major widget selection
  - Important visual reference interpretation
  - Major content architecture

Example (design direction):

```text
I recommend:

A. Premium Minimal
B. Modern Editorial
C. Technology / Futuristic

Recommendation:
B — Modern Editorial

Which direction should I use?
```

Users can **approve, modify, or regenerate** the proposed direction. Approved decisions are recorded in `.website-builder/decisions.md` and become constraints for all later work (see Cross-Skill Rule 2 — never silently override approved decisions).

Two other modes exist for explicit control: **Autonomous** (proceed and log decisions; only ask when overriding an approved decision) and **Manual** (ask before major design and page-structure decisions).

---

## Skill Execution Order

The standard execution order is:

```text
00 → 01 → 02 → 03 → 04 → 05 → 06
→ 07 → 08 → 09 → 10 → 11 → 12
→ 13 → 14 → 15 → 16 → 17 → 18
```

| # | Skill | What it does |
| :--- | :--- | :--- |
| 00 | Orchestration | Project state, execution plan, mode, no-override guard |
| 01 | Project Initialization | Nuxt static foundation, TypeScript, Tailwind, tooling, architecture |
| 02 | Discovery | Company/brand/visual-reference discovery → `content/discovery.ts` |
| 03 | Design System | 3 candidate directions → approved direction + design tokens |
| 04 | Layout System | Shared layout primitives (Container, Section, Grid, Split, Bento…) |
| 05 | Widget Library | Reusable widgets + widget catalog |
| 06 | Animation System | Motion patterns, hierarchy, reduced-motion & performance rules |
| 07 | Homepage | Homepage implementation (placeholder content) |
| 08 | About | About page implementation |
| 09 | Contact | Static contact page (no backend) |
| 10 | Services | `/services` listing + `/services/[slug]` detail |
| 11 | Products | `/products` listing + `/products/[slug]` detail |
| 12 | Legal | `/privacy`, `/terms`, `/cookie-policy`, `/disclaimer` |
| 13 | Content Replacement | Real content replaces placeholders, layout preserved |
| 14 | Responsive | Responsive QA + fixes across all breakpoints |
| 15 | Accessibility | WCAG 2.2 AA audit + remediation |
| 16 | SEO | Titles, metadata, structured data, sitemap, indexability |
| 17 | Performance | Bundle size, images, fonts, animations, CLS, hydration |
| 18 | Visual QA | Screenshot-based final visual validation |

Skills may be rerun when necessary (e.g., QA findings mark a skill `NEEDS_REVISION`). The orchestrator (00) decides what to rerun; the process is a loop until completion criteria are met, not a one-pass pipeline.

---

## Dependencies Between Skills

- **00** depends on nothing; everything depends on 00 for state and mode.
- **01** must run before any implementation skill (07–12).
- **02** must run before **03** (design direction is derived from discovery). Both 02 and 03 read `project-config/` — if `project-config.md` exists, its colors, design direction, logos, and references are primary input, treated like approved user decisions.
- **03** must run before **04**, **05**, **06**, and all page skills (07–12) — they consume design tokens.
- **04** and **05** must run before page skills (07–12) — pages compose layout primitives and widgets.
- **06** must complete before page skills so animations are available.
- **07–12** are independent of each other but are conventionally executed in order.
- **13** must run after the pages exist (07–12) and before final QA (14–18).
- **14–18** are the QA/validation passes; they depend on complete pages (07–13) and can be rerun.
- **18** is the final gate; nothing ships without an acceptable Visual QA.

---

## Widget-Based Design

Pages are composed from **reusable widgets**:

```text
Homepage
├── HeroSplit
├── LogoCloud
├── CompanyIntro
├── ServicesGrid
├── CompanyStats
├── ProcessTimeline
├── Testimonials
└── CTASection
```

The agent uses the **Widget Catalog** — `skills/05-widget-library/widget-catalog.md` — instead of inventing new components unnecessarily. Each page produces a widget list with rationale before implementation; in Assisted/Manual mode, approval is requested first.

A new widget should only be created when an existing widget or variant cannot reasonably satisfy the requirement — and then it is added to the catalog first (recorded decision).

---

## Placeholder Content

Initial website implementation uses **placeholder content**:

```text
Lorem ipsum
Placeholder headline
Placeholder description
Placeholder service
Placeholder product
```

Content is separated from presentation where practical, in structured content sources:

```text
content/
├── site.ts
├── homepage.ts
├── about.ts
├── services.ts
├── products.ts
└── legal.ts
```

Real content is introduced later through `skills/13-content-replacement/skill.md` — it identifies every placeholder, asks for real content where needed, preserves layout integrity, adjusts text length intelligently, and never breaks the design because of content length.

---

## Persistent Project Memory (`.website-builder/`)

This directory is the website's persistent memory. It must not be shared between unrelated websites.

### `state.md`
Tracks the current workflow position:
- Current Skill
- Skill statuses
- Current workflow position
- Blockers
- Next Skill

### `decisions.md`
Stores important approved decisions:
- Design direction
- Colors
- Typography
- Layout
- Widgets
- Navigation
- Animation strategy
- Page/content architecture
- Major technology choices

### `changelog.md`
Chronological record of meaningful work: when, which skill, what changed, why, which files, result.

### `design-history.md`
History of visual design decisions and revisions: initial direction, references, color/typography/layout changes, widget and animation choices, user approvals (`Previous → New → Reason → Approved by user`). Prevents future changes from accidentally reverting approved decisions.

### `qa-history.md`
History of QA results: responsive, accessibility, SEO, performance, visual QA, and build validation (issues → actions → result).

> Note: the skills internally reference `<PROJECT_ROOT>/project-state.md` as the state convention; in the per-website model defined by the runner, this role is fulfilled by `.website-builder/state.md`. The agent maintains state there during execution.

---

## Resuming an Interrupted Workflow

The process is fully **resumable**. If the agent stops after `03-design-system`, the next session does **not** restart from the beginning. The agent:

1. Reads `.website-builder/state.md`.
2. Reads `.website-builder/decisions.md` and recent history.
3. Determines the next incomplete Skill.

Example:

```text
03-design-system = COMPLETED
04-layout-system = IN_PROGRESS
05-widget-library = NOT_STARTED
```

The agent continues from the appropriate point. If state and code conflict (e.g., `state.md` says `07-homepage = COMPLETED` but the homepage is missing), the **actual implementation is the final authority**: the agent marks the Skill `NEEDS_REVISION`, repairs, revalidates, and only then marks it `COMPLETED`.

---

## Future Website Changes

After the website is completed, the system is used to **maintain** it. Example user request:

```text
Change the homepage hero to a more premium design.
```

The agent should:

1. Read `.website-builder/state.md`.
2. Read `.website-builder/decisions.md`.
3. Read `.website-builder/design-history.md`.
4. Read the relevant Skill.
5. Inspect the current implementation.
6. Determine the impact (affected components/pages).
7. Modify only the necessary files.
8. Preserve unrelated approved decisions.
9. Run appropriate QA.
10. Record the change.
11. Update the project state.

> **Do not rerun the entire website-generation workflow for a small change.**

---

## Change Scope

Every future change is classified by scope, and the agent applies the **smallest safe scope**:

```text
LOCAL    — one component or section
PAGE     — one page
SYSTEM   — multiple pages (shared widget/component)
GLOBAL   — design tokens, navigation, layout, shared components
```

Examples:

```text
Change homepage headline      → LOCAL
Change homepage hero layout   → PAGE
Change HeroSplit widget       → SYSTEM
Change primary brand color    → GLOBAL
```

Before modifying anything, identify dependencies; after modifying, validate all affected pages.

---

## When Execution Stops

The agent may pause and wait for the user when the workflow reaches:

```text
WAITING_FOR_USER       — required information or content is missing
WAITING_FOR_APPROVAL   — a meaningful design decision was proposed and awaits approval
BLOCKED                — a blocking error or conflict requires resolution
```

For example, `WAITING_FOR_APPROVAL` means the agent has proposed a meaningful design decision and is waiting for the user. **It must not silently continue with an unapproved major design decision.**

When the user responds, the agent resumes from that exact point — never from the beginning.

---

## Completion Criteria

A website is considered complete **only when**:

```text
✓ Required pages implemented
✓ Design system implemented
✓ Layout system implemented
✓ Widget system implemented
✓ Animations implemented appropriately
✓ Placeholder content replaced when required
✓ Responsive QA completed
✓ Accessibility QA completed
✓ SEO QA completed
✓ Performance QA completed
✓ Visual QA completed
✓ Production build succeeds
✓ No blocking issues remain
```

The agent never claims completion while important work remains; incomplete items are listed explicitly.

---

## Cross-Skill Rules

Every skill MUST follow these eight rules.

### Rule 1 — Preserve the Design System
Never introduce random colors, spacing, typography, radius, or shadows. Use the approved design tokens.

### Rule 2 — Preserve User Decisions
If the user approved a design decision, do not silently replace it.

### Rule 3 — Avoid Generic Templates
Do not repeatedly produce `Navbar → Hero → Three Cards → Testimonials → CTA → Footer` unless it is genuinely appropriate for the brand.

### Rule 4 — Content and Design Are Separate
Placeholder content must be replaceable without rewriting the entire UI.

### Rule 5 — Mobile Is Not an Afterthought
Every widget must define mobile behavior.

### Rule 6 — Accessibility Is Built In
Do not wait until the final audit to consider accessibility.

### Rule 7 — Performance Is Built In
Do not introduce expensive effects merely for visual novelty.

### Rule 8 — No Unnecessary Backend
The website is static. Do not introduce APIs, databases, authentication, or server dependencies.

---

## Execution States

Each skill reports one of:

```text
NOT_STARTED
IN_PROGRESS
WAITING_FOR_USER
WAITING_FOR_APPROVAL
COMPLETED
NEEDS_REVISION
BLOCKED
```

State is recorded in `.website-builder/state.md` (per the runner) — a simple markdown file, not a database. The orchestrator (00) uses it to determine the next skill and to guard against duplicated or conflicting work.

---

## Conventions Used Across Skills

- **Website root:** the website project the runner is executed from.
- **Reusable skills:** copied into `<website>/skills/` (source: the numbered folders in this repository).
- **Project configuration:** `<website>/project-config/` — `project-config.md` (brand settings: colors, direction, content notes) + `brand/` (logos) + `references/` (design samples). Source template: `project-config/` in this repository.
- **Website memory:** `<website>/.website-builder/` — `state.md`, `decisions.md`, `changelog.md`, `design-history.md`, `qa-history.md`.
- **Content sources:** `<website>/content/*.ts` (`site.ts`, `homepage.ts`, `about.ts`, `services.ts`, `products.ts`, `legal.ts`, `discovery.ts`).
- **Design system:** `<website>/design-system/` — `design-direction.md` and the design tokens (CSS variables / Tailwind theme).
- **Layout primitives:** `<website>/components/layout/`.
- **Widgets:** `<website>/components/widgets/`.
- **Animation primitives:** `<website>/components/anim/` and `<website>/composables/`.
- **Placeholders:** markers like `[Placeholder: Company Name]` and Lorem ipsum.
- **Version policy:** never hard-code framework versions; verify the installed version at runtime against current official documentation.
