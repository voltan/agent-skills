# agent-skills

Reusable **AI Agent Skills** for software engineering agents. Each `.md` skill is a complete, self-contained methodology that an AI agent can load and execute against a repository — optimized for stepwise, deterministic, schema-driven execution (e.g., DeepSeek-V4 Flash).

## Architecture

The repository separates **Audit Skills** (assessment/review of existing code and infrastructure) from **Coding Skills** (creation/modification of software — reserved for the future).

```text
agent-skills/
├── backend/                      # Backend skills
│   ├── audit/                    #   Audit skills (existing)
│   │   ├── nestjs/               #     12-skill suite for NestJS/TypeScript
│   │   └── mezzio/               #     12-skill suite for PHP 8.x / Mezzio / Laminas
│   └── coding/                   #   Reserved for future coding skills
│       ├── nestjs/
│       └── mezzio/
├── frontend/                     # Frontend skills
│   ├── audit/                    #   Audit skills (existing)
│   │   ├── nuxtjs/               #     Flat Nuxt audit skill set (10 skills)
│   │   └── vuejs/                #     Flat Vue audit skill set (9 skills)
│   └── coding/                   #   Reserved for future coding skills
│       ├── nuxtjs/
│       └── vuejs/
├── seo/                          # SEO & web performance skills
│   ├── audit/                    #   Audit skill (nuxt-seo-performance-audit-agent)
│   └── coding/                   #   Reserved for future coding skills
├── infrastructure/               # Infrastructure & deployment skills
│   ├── audit/                    #   Audit skill (infrastructure security audit)
│   └── coding/                   #   Reserved for future coding skills
├── reports/                      # Execution output (generated artifacts, git-ignored)
├── README.md
└── .gitignore
```

## Audit vs Coding

| Namespace | Purpose | Status |
| :--- | :--- | :--- |
| `backend/audit/` | Assessment of NestJS and Mezzio/Laminas backends | ✅ 24 skills |
| `frontend/audit/` | Assessment of Nuxt and Vue frontends | ✅ 19 skills |
| `seo/audit/` | SEO & web performance assessment | ✅ 1 skill |
| `infrastructure/audit/` | Infrastructure & deployment security assessment | ✅ 1 skill |
| `*/coding/` | Future implementation skills (create/modify software) | 🚧 Empty placeholders |

**Key distinction:** a skill remains an **Audit Skill** even when it recommends fixes — it analyzes code, identifies issues, provides evidence, rates severity, and proposes remediation. Coding skills, whose primary purpose is to create or modify software, do not exist yet; their directories are tracked via minimal `README.md` files only.

## Audit Suites

Each audit namespace follows a consistent methodology:

- **7-phase execution lifecycle** — workspace verification, report initialization, incremental resume, exhaustive analysis, progressive disk persistence, execution-log update, final summary.
- **Standardized finding model** — every finding includes ID, Severity, Category, Title, Description, Impact, Affected Files, Evidence, Root Cause, Attack/Failure Scenario, Remediation, Validation, Cross-Layer Impact, References.
- **Severity model** — `CRITICAL | HIGH | MEDIUM | LOW | INFO` (+ `BLOCKER` where applicable).
- **Evidence-based** — no finding without concrete evidence (file, line, code/behavior excerpt).
- **Cross-layer auditing** — frontend findings that depend on backend behavior are tagged `Cross-Layer` and reference the relevant backend skill (e.g., a frontend route guard is never treated as authorization; the backend must enforce it).
- **Reports** — execution artifacts are written to `reports/YYYY-MM-DD/` and are intentionally excluded from version control.

## Quick Reference

| Path | Content |
| :--- | :--- |
| `backend/audit/nestjs/` | Skills 1–12: security, performance, TypeORM/database, compliance, QA, observability/SRE, CI/CD, async architecture, resilience/multi-tenancy, RAG/vector/LLM, master consolidation, multi-stack security |
| `backend/audit/mezzio/` | Skills 1–12: the Mezzio/Laminas PHP 8.x equivalents of the NestJS suite |
| `frontend/audit/nuxtjs/` | `1-audit.md` (entry point) + `2-security`, `3-performance`, `4-architecture`, `5-ssr`, `6-seo`, `7-api`, `8-infrastructure`, `9-dependencies`, `10-testing` |
| `frontend/audit/vuejs/` | `1-audit.md` (entry point) + `2-security`, `3-performance`, `4-architecture`, `5-rendering`, `6-api`, `7-infrastructure`, `8-dependencies`, `9-testing` |
| `seo/audit/` | `nuxt-seo-performance-audit-agent.md` |
| `infrastructure/audit/` | `infrastructure-repo-security-audit-skill.md` |

> **Version policy:** skills never hard-code framework versions; agents verify the installed version at audit time against current official documentation (e.g., Nuxt 4.x, Vue 3.5.x).
