# Nuxt Dependencies & Supply-Chain Audit Skill

> **Frontend Audit Suite — Enhanced for DeepSeek-V4 Flash.** Stepwise, deterministic, schema-driven.

## Purpose

Audit the Nuxt dependency tree: framework and module versions, lockfile integrity, known vulnerabilities, abandoned/malicious packages, dependency confusion, install scripts, and bundle bloat.

## Scope

Nuxt, Vue, Vite, Nitro, Nuxt modules, npm/pnpm/yarn packages, lockfiles (`package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`), known vulnerabilities, abandoned dependencies, malicious packages, dependency confusion, install scripts, postinstall scripts, excessive dependencies, unnecessary runtime dependencies.

## Framework Context

Nuxt 4 is built on Vue 3.5, Vite, and Nitro; modules extend it (`@nuxtjs/security`, `@pinia/nuxt`, `@nuxt/image`, `@nuxtjs/seo`, `@nuxt/content`, etc.). The ecosystem releases frequently; security advisories are published via npm/GitHub.

**Version verification (MANDATORY):** record `nuxt`, `vue`, `vite`, `nitropack` versions from `package.json`/lockfile, then compare against current stable on `https://nuxt.com/` and `https://vuejs.org/` and against npm advisories. Nuxt 3 is EOL — HIGH upgrade finding. Deprecated modules/APIs (e.g., legacy module names, removed Nuxt 2-era packages) are findings.

## Preconditions

1. Repository read access; record commit hash.
2. Reports at `reports/YYYY-MM-DD/nuxt-08-dependencies-review.md`; shared log initialized.
3. Package manager identified (npm/pnpm/yarn/bun) from lockfile presence.

## Audit Objectives

1. Verify framework versions are current/patched and supported (not EOL).
2. Identify known vulnerabilities via the package manager's audit command.
3. Identify abandoned, unmaintained, or suspicious packages and install-script risk.
4. Identify excessive/unnecessary dependencies and bundle contributors.
5. Verify lockfile integrity and reproducible installs.

## Audit Rules

1. **Known vulnerabilities:** run the audit command for the installed package manager: `npm audit`, `pnpm audit`, `yarn audit`, or `bun audit`. Every HIGH/CRITICAL advisory with an available fix is a finding (severity per advisory, typically HIGH). Advisories without a fix: MEDIUM with a mitigation note.
2. **Outdated/EOL frameworks:** Nuxt 3 (EOL July 31, 2026) — HIGH; Nuxt 2 — CRITICAL (unsupported, unpatched); Vue 2 — HIGH (EOL); outdated Nuxt 4.x with known fixed CVEs — HIGH if patched versions exist.
3. **Deprecated/legacy APIs & modules:** flag usage of removed Nuxt APIs/modules (e.g., Nuxt 2-era packages, renamed modules, `@nuxtjs/proxy` alternatives) — MEDIUM; flag packages marked deprecated on npm — MEDIUM.
4. **Abandoned packages:** flag packages with no recent releases/stale maintenance (no updates in 12+ months for active-use packages) — LOW/MEDIUM; verify against npm metadata, not assumption.
5. **Malicious/suspicious packages:** flag typosquatting candidates (names similar to popular packages), packages with install scripts that fetch/execute remote code, or known-malicious package names from advisory feeds (e.g., GitHub Advisory Database, Socket.dev reports) — CRITICAL/HIGH. Inspect `postinstall`/`preinstall`/`prepare` scripts of non-dev dependencies for suspicious behavior.
6. **Install scripts:** flag unnecessary `postinstall` scripts in runtime dependencies (supply-chain risk); verify scripts are required and benign; pin package versions and use lockfiles.
7. **Dependency confusion:** flag any package whose name collides with an internal/private package name and could resolve from the public registry; verify registry scope config (`@scope:registry`) and `resolutions`/overrides.
8. **Lockfile integrity & reproducibility:** flag missing lockfile (CRITICAL for reproducibility), lockfile/package.json drift (package.json changed without lockfile update), floating ranges (`^`/`~` on security-critical deps), missing `packageManager` field / corepack pinning — MEDIUM.
9. **Excessive dependencies:** flag packages pulled in only for small utilities (left-pad-style), duplicate packages resolving multiple versions, unused deps (verify with `knip`/`depcheck`), and dev-only packages in production `dependencies` — LOW/MEDIUM. Runtime deps that should be devDeps (build tools) bloat production installs.
10. **Bundle contributors:** flag large runtime deps that end up in the client bundle unnecessarily (e.g., server-only libs imported client-side) — MEDIUM; coordinate with `3-performance.md`.
11. **Module hygiene:** flag Nuxt modules that are unmaintained, load heavy runtime code, or duplicate functionality of other installed modules — MEDIUM.

## Detection Logic

1. Read `package.json` + lockfile; record direct dependency list and framework versions.
2. Run the package manager audit command; collect advisories (package, severity, fix availability).
3. Inspect `node_modules` metadata for deprecation/abandonment signals (`npm view <pkg> time`/`dist-tags`), and scan `install`/`postinstall` scripts.
4. Run an unused-dependency check (knip/depcheck) if available.
5. Cross-reference module names and versions against official Nuxt module catalog and advisories.

## Evidence Requirements

- The audit command output excerpt (package, severity, CVSS, fix version).
- package.json/lockfile excerpts with file/line for version and script findings.
- npm metadata evidence for deprecation/abandonment claims.
- Install-script content excerpt for suspicious scripts.

## Severity

- CRITICAL: known-malicious package, dependency-confusion exposure, Nuxt 2 (unpatched), exploitable supply-chain script.
- HIGH: Nuxt 3 EOL, HIGH/CRITICAL advisories with fixes, Vue 2.
- MEDIUM: outdated Nuxt 4 with fixed CVEs, deprecated/abandoned packages, lockfile drift, missing audit gates.
- LOW/INFO: unused deps, bundle bloat, minor hygiene.

## False Positives

- A `postinstall` script is not automatically malicious — inspect before reporting.
- Old-but-maintained packages are not abandoned — verify activity before flagging.
- `npm audit` false-positive advisories for dev-only tooling in non-production contexts — note context in the finding.
- A package with no recent releases may be stable/completed — evaluate usage and risk, not age alone.

## Remediation

- Upgrade to current Nuxt 4.x; migrate off EOL majors.
- Apply available advisory fixes (`npm audit fix`, version bumps); document un-fixable advisories with mitigations.
- Add CI gates: `npm audit` (fail on high), lockfile diff check, dependency update automation (Renovate/Dependabot).
- Remove unused deps; move build-only tools to devDependencies; pin versions; enforce lockfiles.

## Validation

Re-run the audit command after fixes; verify zero HIGH/CRITICAL advisories (or documented exceptions); verify lockfile consistency; re-run build/tests.

## Cross-Layer Considerations

- Dependency versions shared with the backend (shared types, SDK packages) — coordinate version bumps with backend skills.
- Server-side Nuxt deps may interact with Nitro runtime — verify deployment compatibility (`8-infrastructure.md`).

## References

- https://github.com/nuxt/nuxt/releases (EOL/security), https://nuxt.com/modules (module catalog)
- npm audit docs, GitHub Advisory Database, https://socket.dev/ (supply-chain scanning)

## Report & Log Integration

Progressive persistence to `reports/YYYY-MM-DD/nuxt-08-dependencies-review.md`; shared log block; shared finding schema.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** Editorial context; the executing model MUST ignore this section.

The supply-chain rules emphasize evidence over alarmism: the skill distinguishes "advisory with a fix" (actionable) from "advisory without a fix" (mitigate), and refuses to flag postinstall scripts or old packages without inspection. The EOL policy is version-anchored (Nuxt 3 → HIGH, Nuxt 2 → CRITICAL), which keeps severity defensible. CI-gate recommendations close the loop so the audit findings translate into permanent protection.
