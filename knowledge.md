# knowledge.md — agent-skills

## What this is

A repository of **reusable AI Agent Skills**: self-contained markdown methodologies that an AI coding agent loads and executes against a target repository. This repo contains **no application code** — it is pure markdown (no source files). There is no build, test, lint, or runtime tooling of any kind.

Each skill describes **how to audit or build** something; the repo is the *source* of those methodologies. Audit skills analyze target codebases and produce findings; coding skills instruct an agent to build/modify software.

## Where key code lives

- `backend/audit/nestjs/` — 11-skill audit suite for NestJS/TypeScript (files `1-security-audit.md` … `11-master-consolidation-audit.md`).
- `backend/audit/mezzio/` — 11-skill audit suite for PHP 8.x / Mezzio / Laminas (files `1-security-vulnerability-audit.md` … `11-master-consolidation-report.md`).
- `frontend/audit/nuxtjs/` — flat 10-file suite; **`1-audit.md` is the entry point** defining execution order, rest are `2-security.md` … `10-testing.md`.
- `frontend/audit/vuejs/` — flat 9-file suite; `1-audit.md` entry point, `2-security.md` … `9-testing.md` (note: `5-rendering.md`, not `5-ssr.md`).
- `seo/audit/nuxt-seo-performance-audit-agent.md` — SEO/performance audit skill.
- `infrastructure/audit/infrastructure-repo-security-audit-skill.md` — infra/deployment security audit (Docker, NGINX, TLS, NATS, Redis, PostgreSQL).
- `frontend/coding/nuxtjs/company-website-static/` — **the only implemented coding skill**: a Nuxt static corporate-website builder. Reusable skills live in `skills/` (numbered folders `00-orchestrator/` … `18-visual-qa/`, plus `19-figma-to-nuxt/` — a scenario-based support skill for Figma-link/screenshot-driven implementation, loaded only when a Figma source is provided). Per-project brand input lives in `project-config/` (`project-config.example.md` template with colors/design direction, `brand/` for logos, `references/` for design samples, and a Design Source section for pasting a Figma URL). Entry point for execution is `run-website-builder.md` (the "runner"); `README.md` documents the whole system.
- `backend/coding/`, `frontend/coding/vuejs/`, `seo/coding/`, `infrastructure/coding/` — placeholders (README only).

## Commands

**There are no commands.** No `package.json`, Makefile, scripts, or lockfiles exist anywhere in the repo. Nothing to install, build, test, or lint. Changes are markdown edits only.

## Conventions & gotchas

- **Root `README.md` is stale**: it claims all `*/coding/` trees are "empty placeholders" — but `frontend/coding/nuxtjs/company-website-static/` is a fully implemented coding skill system. `frontend/coding/README.md` likewise says "No coding skills exist yet." Trust the actual directories over these docs.
- **Version policy**: skills must never hard-code framework versions; agents verify installed versions at audit time against current official docs (e.g., Nuxt 4.x, Vue 3.5.x).
- **Standardized finding model**: every audit finding carries ID, Severity, Category, Title, Description, Impact, Affected Files, Evidence, Root Cause, Attack/Failure Scenario, Remediation, Validation, Cross-Layer Impact, References. Evidence-based — no finding without file/line/code excerpt.
- **Severity model**: `CRITICAL | HIGH | MEDIUM | LOW | INFO` (+ `BLOCKER` where applicable).
- **Cross-layer rule**: a frontend route guard is never treated as authorization — backend must enforce it; frontend findings depending on backend behavior are tagged `Cross-Layer` and reference the relevant backend skill.
- **Audit execution lifecycle**: 7 phases — workspace verification, report init, incremental resume, exhaustive analysis, progressive disk persistence, execution-log update, final summary.
- **`reports/` is git-ignored** (see `.gitignore`, along with `.idea` and `.freebuff`). Audit artifacts are written to `reports/YYYY-MM-DD/` and intentionally never committed. Frontend report names are framework-prefixed (`nuxt-02-security-review.md`) to avoid colliding with backend names (`02-*`).
- **Naming quirks**: backend NestJS files use `-audit.md` suffixes; Mezzio files mix `-audit.md` and `-report.md`/`-scorecard.md` suffixes. Frontend files use plain `2-security.md` style.
- **Coding-skill workflow model** (`company-website-static`) — three concepts, strictly separated: `skills/` (HOW to work — reusable, copied into each website), `project-config/` (WHAT the site should look like — per-project colors, direction, logos, references, Figma URL; treated as user input, never silently overridden), and `.website-builder/` (WHAT happened — `state.md`, `decisions.md`, `changelog.md`, `design-history.md`, `qa-history.md`). Never write website-specific decisions into skill files. When a Figma URL is provided, `skills/19-figma-to-nuxt/skill.md` governs: the Figma becomes the visual source of truth (analysis → `.website-builder/figma-implementation.md` → tokens → pixel-accurate implementation → screenshot comparison at matching viewport sizes). Execution is resumable via `state.md`; the actual implementation outranks recorded state on conflicts.
- Markdown is plain — no mkdocs/docusaurus toolchain, no internal link checker configured.
