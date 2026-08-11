# Vue Dependencies & Supply-Chain Audit Skill

> **Frontend Audit Suite — Enhanced for DeepSeek-V4 Flash.** Stepwise, deterministic, schema-driven.

## Purpose

Audit the Vue dependency tree: framework and ecosystem versions, lockfile integrity, known vulnerabilities, abandoned/malicious packages, dependency confusion, install scripts, and bundle-relevant dependency bloat.

## Scope

Vue, Vue Router, Pinia, Vite, plugins, npm/pnpm/yarn dependencies, lockfiles (`package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`), vulnerable packages, abandoned packages, install scripts, dependency confusion, malicious packages, excessive dependencies.

## Framework Context

Vue 3.5 + Vue Router 4 + Pinia 3 (current majors); Vite as the build tool. Ecosystem packages frequently have CVEs and compatibility constraints; Vue 2 (EOL Dec 31, 2023) receives no security fixes.

**Version verification (MANDATORY):** record `vue`, `vue-router`, `pinia`, `vite` versions from `package.json`/lockfile, compare against current stable on `https://vuejs.org/` and npm, and check advisories. Vue 2 → HIGH (EOL); Vue 2 with known exploitable issues → CRITICAL. Deprecated packages (e.g., old Vue 2 plugins) are findings.

## Preconditions

1. Repository read access; record commit hash.
2. Reports at `reports/YYYY-MM-DD/vue-07-dependencies-review.md`; shared log initialized.
3. Package manager identified from lockfile presence.

## Audit Objectives

1. Verify framework versions are current/patched (not EOL).
2. Identify known vulnerabilities via the package manager's audit command.
3. Identify abandoned/malicious/suspicious packages and install-script risk.
4. Identify dependency confusion and lockfile/reproducibility issues.
5. Identify unnecessary dependencies and bundle contributors.

## Audit Rules

1. **Known vulnerabilities:** run `npm audit` / `pnpm audit` / `yarn audit` / `bun audit`. HIGH/CRITICAL advisories with fixes → findings (severity per advisory); without fixes → MEDIUM + mitigation.
2. **EOL frameworks:** Vue 2 → HIGH (EOL, no security fixes); Vue 3.x outdated with fixed CVEs → HIGH if patched versions exist.
3. **Deprecated/legacy packages:** flag deprecated npm packages and Vue 2-era plugins still in use — MEDIUM.
4. **Abandoned packages:** flag packages with no meaningful maintenance (12+ months stale for active-use deps) — LOW/MEDIUM; verify against npm metadata.
5. **Malicious/suspicious packages:** flag typosquatting candidates, packages with install scripts fetching/executing remote code, known-malicious names from advisory feeds (GitHub Advisory Database, Socket.dev) — CRITICAL/HIGH; inspect `postinstall`/`preinstall`/`prepare` scripts of runtime deps.
6. **Install scripts:** flag unnecessary postinstall scripts in runtime dependencies — supply-chain risk; verify scripts are required and benign; pin versions and use lockfiles.
7. **Dependency confusion:** flag packages whose names collide with private/internal packages that could resolve from the public registry; verify registry scoping and `resolutions`/overrides.
8. **Lockfile integrity & reproducibility:** flag missing lockfile (CRITICAL), package.json/lockfile drift, floating ranges on security-critical deps, missing `packageManager`/corepack pinning — MEDIUM.
9. **Excessive dependencies:** flag unused deps (`knip`/`depcheck`), duplicate versions of the same package, dev-only tools in production `dependencies` — LOW/MEDIUM.
10. **Bundle contributors:** flag large runtime deps unnecessarily bundled client-side — MEDIUM (coordinate with `3-performance.md`).

## Detection Logic

1. Read `package.json` + lockfile; record direct deps and versions.
2. Run the audit command; collect advisories (package, severity, fix availability).
3. Inspect `postinstall` scripts and npm metadata (deprecation/activity signals).
4. Run an unused-dep check if available; analyze bundle contributors via build analysis.
5. Cross-reference against GitHub Advisory Database for known-malicious packages.

## Evidence Requirements

- Audit command output excerpt (package, severity, CVSS, fix version).
- package.json/lockfile excerpts with file/line.
- npm metadata evidence for deprecation/abandonment claims.
- Install-script content excerpt for suspicious scripts.

## Severity

- CRITICAL: known-malicious package, dependency-confusion exposure, Vue 2 with exploitable issues, supply-chain script executing remote code.
- HIGH: Vue 2 EOL, HIGH/CRITICAL advisories with fixes.
- MEDIUM: deprecated/abandoned packages, lockfile drift, missing audit gates, outdated Vue 3 with fixed CVEs.
- LOW/INFO: unused deps, bundle bloat, hygiene.

## False Positives

- A postinstall script is not automatically malicious — inspect before reporting.
- Old-but-maintained packages are not abandoned — verify activity.
- Dev-only audit findings matter less for the shipped bundle — note context.
- Vue 2 in a legacy app with a migration plan is still HIGH (EOL), not LOW — no false-positive discount for popularity.

## Remediation

- Upgrade to current Vue 3.5 + Vue Router 4 + Pinia 3; migrate off Vue 2.
- Apply available fixes (`npm audit fix`, version bumps); document un-fixable advisories.
- Add CI gates: audit (fail on high), lockfile consistency check, Renovate/Dependabot.
- Remove unused deps; pin versions; enforce lockfiles.

## Validation

Re-run the audit command after fixes; verify zero HIGH/CRITICAL (or documented exceptions); verify lockfile consistency; re-run build/tests.

## Cross-Layer Considerations

- Shared packages between frontend and backend (types, SDKs) — coordinate version bumps with backend skills.
- Plugin/dependency versions affecting Vite builds interact with `7-infrastructure.md`.

## References

- https://vuejs.org/about/releases (support policy), https://github.com/vuejs/core/releases
- npm audit docs, GitHub Advisory Database, https://socket.dev/

## Report & Log Integration

Progressive persistence to `reports/YYYY-MM-DD/vue-07-dependencies-review.md`; shared log block; shared finding schema.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** Editorial context; the executing model MUST ignore this section.

The version-anchored EOL policy (Vue 2 → HIGH, never discounted for legacy popularity) is the deliberate hard line — Vue 2's EOL is the single most common real-world Vue supply-chain risk. The evidence-over-alarmism stance on install scripts and abandonment mirrors the Nuxt dependency skill so both frameworks enforce the same standard.
