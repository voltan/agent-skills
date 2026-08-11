# Project Configuration (`project-config/`)

Per-project **design and brand configuration** for the Nuxt.js Static Corporate Website Builder.

This folder is the third concept of the system (see the main README):

| Concept | Role | Where |
| :--- | :--- | :--- |
| `skills/` | **HOW to work** — reusable instructions | copied into each website |
| `project-config/` | **WHAT the site should look like** — brand input: colors, design direction, logos, reference images, content notes | copied into each website, then filled in |
| `.website-builder/` | **WHAT happened** — state, decisions, history | created per website during execution |

## What it contains

```text
project-config/
├── README.md                    ← this document
├── project-config.example.md    ← the template (copy → fill → rename)
├── brand/                       ← put logo / favicon / brand asset files here
└── references/                  ← put design sample images (screenshots, mockups) here
```

## How to use it per project

1. **Copy** this whole folder into the website project root (next to `skills/`).
2. **Rename** `project-config.example.md` → `project-config.md`.
3. **Fill it in** — colors, typography, design direction, content notes. **To provide a design as a Figma link, paste the URL in the Design Source section** (or drop screenshots into `references/`). Leave anything unknown empty (the agent will propose it in Skill 02/03).
4. **Drop assets in:**
   - Logos → `project-config/brand/` (e.g. `logo.svg`, `logo-dark.svg`, `favicon.svg`)
   - Design samples / screenshots / mockups → `project-config/references/` (e.g. `hero-style-01.jpg`)
5. Reference those files **by relative path** in `project-config.md`.

## How agents consume it

- **Skill 01 (Project Init)** verifies the folder exists and scaffolds a copy of the template if missing.
- **Skill 02 (Discovery)** reads `project-config/project-config.md` **first** and never re-asks for anything already provided there.
- **Skill 03 (Design System)** uses the configured colors, typography, and design direction as the **base palette** and honors it like a user decision.
- **Figma (`skills/19-figma-to-nuxt/`)** — if the config declares a **Figma URL** (or the user provides one), the agent loads the Figma-to-Nuxt skill: the Figma design becomes the visual source of truth, the agent analyzes it (or requests screenshots when it cannot open it), and Skills 02/03 build on that analysis.
- **Skills 07–12 (Pages)** use the configured logo and brand assets.
- **Skills 13–18 (Content & QA)** use the content notes, logo (SEO), and reference images (Visual QA) from the config.

## Rules

- Config values are **user-provided input**: treat them like approved decisions — never silently override them (Cross-Skill Rule 2).
- The config is **input**, not a decision log. Approvals and changes still go to `.website-builder/decisions.md` and `.website-builder/design-history.md`.
- Never copy copyrighted logos, brand assets, or design samples into a website. If a reference is copyrighted, describe the style principles instead of copying the file.
- If `project-config.md` is missing, the workflow continues: the agent asks the user directly and records answers in discovery (02) as usual.
