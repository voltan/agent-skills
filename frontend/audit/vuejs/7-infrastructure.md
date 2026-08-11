# Vue Infrastructure & Deployment Audit Skill

> **Frontend Audit Suite — Enhanced for DeepSeek-V4 Flash.** Stepwise, deterministic, schema-driven.

## Purpose

Audit the build and delivery infrastructure around a Vue SPA: Vite build configuration, Node toolchain, static hosting/CDN, reverse proxy, TLS and security headers, caching, environment configuration, and observability.

## Scope

Vite, Node, Nginx, CDN, Docker, TLS, HTTPS, HSTS, CSP, security headers, CORS, cache, compression, asset delivery, environment configuration, observability, deployment configuration.

## Framework Context

Vue SPAs build with Vite to static assets (`dist/`) served by a CDN/static host or behind Nginx — the SPA has no server runtime (unless SSR). Because there is no app server, headers, caching, and redirects are configured at the edge (CDN/proxy/host config). SPA routing requires a fallback rewrite to `index.html` (history mode) — a common misconfiguration source.

**Version verification (MANDATORY):** see `1-audit.md`.

## Preconditions

1. Repository read access; record commit hash.
2. Reports at `reports/YYYY-MM-DD/vue-06-infrastructure-review.md`; shared log initialized.
3. Deployment configs: `vite.config.*`, Dockerfile (if any), Nginx config, CDN/host config, CI deploy steps; a deployed instance preferred for effective-header verification.

## Audit Objectives

1. Verify Vite build configuration (base path, asset hashing, env handling) is correct and secure.
2. Verify TLS/security headers/CORS at the effective edge.
3. Verify caching of static assets and the SPA fallback behavior.
4. Verify env configuration has no secrets and no committed `.env` files.
5. Verify observability for a static SPA (error reporting, RUM) is present.

## Audit Rules

### Build & env
1. **Vite config hygiene:** flag missing asset hashing (`filename`/`chunkFileNames` hashes — affects cache busting), missing `base` for subpath deployments, missing `build.target` awareness (legacy browser support vs modern), `define` misuse for secrets (inlined values ARE exposed in the bundle) — MEDIUM/CRITICAL for secrets.
2. **Env handling:** flag committed `.env` files (CRITICAL if secrets), `VITE_*` vars containing secrets (CRITICAL — they ship to the client), unvalidated env access (`import.meta.env.VITE_*` without required-check). Distinguish public config (API base URL) from secrets.
3. **Docker hygiene (if used):** flag unpinned base images, root user, missing `.dockerignore` (node_modules, dist, .env), `npm ci` vs `npm install`, multi-stage builds — MEDIUM (see Nuxt `7-infrastructure.md` for the equivalent detailed rules).
4. **Node version:** flag outdated Node for the Vite/Vue version in use — LOW/MEDIUM.

### Delivery & edge
5. **SPA fallback:** flag missing history-mode fallback (direct deep-link to a route returns 404/500 or serves wrong file) — HIGH for SPA UX; verify `try_files ... /index.html` on Nginx or host equivalent, and that it does NOT apply to `api/` or asset paths.
6. **TLS/HSTS:** flag HTTP allowed in production, missing HSTS, missing HTTPS redirect — HIGH/MEDIUM; verify at the edge.
7. **Security headers:** verify effective headers on real responses: `Content-Security-Policy`, `Strict-Transport-Security`, `X-Content-Type-Options`, `Referrer-Policy`, `Permissions-Policy`, `Cross-Origin-Opener-Policy`, `Cross-Origin-Resource-Policy`, `Cross-Origin-Embedder-Policy`. CSP for an SPA must allow its own hashed bundles (integrity) — flag overly-permissive CSP (`unsafe-inline` for scripts without justification) — MEDIUM/HIGH. Do not demand every header blindly (evaluate architecture; COEP/COOP have real costs).
8. **CORS:** an SPA's CORS needs are controlled by the backend — `Cross-Layer`; verify the backend allows only intended origins with credentials.
9. **Caching of static assets:** verify hashed assets are immutable-cached (`Cache-Control: public, max-age=31536000, immutable`) and `index.html` is `no-cache`/short-TTL — MEDIUM; flag missing cache headers causing re-fetches or stale deployments.
10. **Compression:** verify Brotli/gzip for text assets at the edge — MEDIUM/LOW.
11. **HTTP/2/3:** verify enabled where the provider supports it — LOW (perf note).

### Observability
12. **Error monitoring:** flag no client-side error reporting (Sentry/Bugsnag or equivalent) for production — MEDIUM; RUM (Core Web Vitals collection) absent — LOW/MEDIUM.
13. **Health/deployment verification:** flag no deployment health check (SPA often just checks 200 + content hash) — LOW; flag missing rollback strategy — LOW.

## Detection Logic

1. Read `vite.config.*`, `.env*`, Dockerfile/Nginx configs, CI deploy steps.
2. Capture effective headers/cache behavior on the deployed origin (`curl -sI` on `/`, a hashed asset, and a deep route).
3. Check for secrets in `VITE_*` usage and committed env files.
4. Verify the SPA fallback with a deep-link request.

## Evidence Requirements

- Config excerpts with file/line.
- Captured response headers (security, cache) and deep-link status code.
- Built-bundle evidence for env-secret exposure (`grep` of `dist/` assets for the secret).

## Severity

- CRITICAL: secrets in client bundle / committed `.env`.
- HIGH: missing history-mode fallback, HTTP-only production, dangerously permissive CSP on a user-content app.
- MEDIUM: missing security headers, weak static caching, no error monitoring, Docker hygiene.
- LOW/INFO: compression/HTTP2 notes, Node version.

## False Positives

- A static SPA legitimately has no `/health` endpoint or app server — evaluate by hosting model.
- Missing headers may be set by the CDN — verify effective headers before reporting.
- CSP with `unsafe-inline` styles is sometimes required by third-party widgets — evaluate the justification.
- `base` default is fine for root deployments — only flag when subpath deployment misconfigures it.

## Remediation

- Configure SPA fallback, immutable asset caching, and correct cache-control for `index.html` at the edge.
- Set security headers at the CDN/proxy; enforce HTTPS + HSTS.
- Move secrets server-side; validate `VITE_*` usage; add `.env` to `.gitignore` and purge history if committed.
- Add client error monitoring and RUM.

## Validation

Re-capture headers/status codes after fixes; verify deep links work; verify no secrets in the built bundle; re-run the deployment.

## Cross-Layer Considerations

- CORS policy lives at the backend — `Cross-Layer` with backend security skills.
- Cache-control of API responses is backend-owned — coordinate with `6-api.md` and backend infra skills (`backend/audit/nestjs/7-cicd-infrastructure.md`, `backend/audit/mezzio/mezzio-cicd-infrastructure-audit.md`).
- Static hosting may share an origin with the backend (same CDN/domain) — verify routing separation.

## References

- https://vitejs.dev/guide/env-and-mode, https://vitejs.dev/config
- https://web.dev/articles/http-cache, https://owasp.org/www-project-secure-headers/

## Report & Log Integration

Progressive persistence to `reports/YYYY-MM-DD/vue-06-infrastructure-review.md`; shared log block; shared finding schema.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** Editorial context; the executing model MUST ignore this section.

Because a Vue SPA has no app server, this skill's center of gravity is the edge: fallback rewrites, immutable asset caching, and effective headers. The SPA-fallback rule addresses the most common production failure (deep links 404ing), and the `VITE_*` secret rule mirrors the Nuxt `runtimeConfig.public` doctrine — both frameworks ultimately suffer the same client-bundle secret exposure, just through different channels.
