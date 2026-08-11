# Nuxt Infrastructure & Deployment Audit Skill

> **Frontend Audit Suite — Enhanced for DeepSeek-V4 Flash.** Stepwise, deterministic, schema-driven.

## Purpose

Audit the deployment and infrastructure around a Nuxt application: containerization, Nitro deployment targets, reverse proxy/CDN configuration, TLS and security headers, caching layers, runtime environment, and observability.

## Scope

Docker, Node runtime, Nitro deployment (node-server / edge / presets), Nginx, reverse proxy, CDN, TLS/HTTPS, HSTS, CSP, security headers, CORS, cache headers, compression (Brotli/gzip), HTTP/2, HTTP/3, WebSocket, SSE, proxy timeouts, request limits, response buffering, environment configuration, production runtime, observability, logging, tracing, correlation IDs, health checks.

## Framework Context

Nuxt 4 deploys via Nitro presets: `node-server` (default), edge (Cloudflare Workers/Deno), `vercel`, `netlify`, `aws-lambda`, static (`prerender`). Deployment config in `nuxt.config.ts` (`nitro.preset`, `nitro.routeRules`, `nitro.prerender`) plus `Dockerfile`, Nginx config, CDN settings. Node version matters (Nuxt 4 requires Node ≥ 18/20+).

**Version verification (MANDATORY):** see `1-audit.md`.

## Preconditions

1. Repository read access; record commit hash.
2. Reports at `reports/YYYY-MM-DD/nuxt-07-infrastructure-review.md`; shared log initialized.
3. Access to deployment artifacts (Dockerfile, Nginx config, CDN/edge config, CI deploy steps) — at least source-of-truth configs; a deployed instance for header/cache verification is preferred.

## Audit Objectives

1. Verify the Nitro deployment target matches the hosting model and is configured safely.
2. Verify TLS/security headers/CORS at the effective edge (not just source config).
3. Verify the caching chain (browser → CDN → proxy → Nitro) is safe and effective.
4. Verify runtime environment (Node version, env config, resource limits) and observability.

## Audit Rules

### Build & runtime
1. **Docker hygiene:** flag `node:latest`/unpinned base images; missing `.dockerignore` (node_modules, `.nuxt`, reports, secrets); running as root instead of a non-root user; missing multi-stage build; `npm ci` vs `npm install`; missing `NODE_ENV=production`; dev dependencies in the production image.
2. **Node version:** flag an outdated/unsupported Node runtime for the installed Nuxt version (Nuxt 4 requires a current LTS/active Node line); mismatch between local and production Node versions.
3. **Nitro preset match:** flag a deployment config mismatched with the preset (e.g., node-server preset behind a CDN expecting edge; static output with dynamic routes). Verify `nitro.preset`/hosting adapter correctness.
4. **Env configuration:** flag production `.env` committed to the repo (CRITICAL), unvalidated env vars (missing fail-fast on required `NUXT_*` secrets), and env vars leaking to the client (see `2-security.md`).
5. **Resource limits:** flag missing memory/timeout limits for the Node process (OOM restarts), missing worker/process scaling guidance for the hosting model.

### Proxy, CDN & TLS
6. **TLS/HSTS:** flag HTTP allowed in production, missing HSTS (with `includeSubDomains; preload` where appropriate), missing redirect to HTTPS; verify certificate configuration on the proxy/CDN.
7. **Security headers at the edge:** verify effective `Content-Security-Policy`, `X-Content-Type-Options: nosniff`, `Referrer-Policy`, `Permissions-Policy`, `Cross-Origin-Opener-Policy` etc. on real responses (see `2-security.md` for the header matrix; do not demand every header blindly).
8. **CORS:** verify CORS at the edge matches the app's origins and backend policy (`Cross-Layer` with backend CORS).
9. **Proxy timeouts/limits:** flag missing proxy timeouts (long-running SSR hangs), missing request-size limits, response buffering misconfig; flag WebSocket/SSE proxy timeouts that break long-lived connections.
10. **Compression:** flag missing Brotli/gzip compression at the CDN/proxy for text assets; verify cache + compression interaction.
11. **HTTP/2/3:** verify enabled at the edge where the provider supports it (performance note, not a defect per se).

### Caching chain
12. **Cache correctness:** verify the full chain (browser → CDN → reverse proxy → Nitro) honors route rules: public routes cacheable with correct `Cache-Control`, private/authenticated routes `private`/`no-store`. Public caching of authenticated data is CRITICAL (see `5-ssr.md`/`2-security.md`).
13. **Cache key correctness:** verify CDN/proxy cache keys include the parts that change the response (language, variant); flag cookie-less caching of personalized responses.
14. **Purge/invalidation:** verify a cache purge/invalidation strategy exists for CDN/Nitro caches on deploys; flag ISR/SWR staleness without purge paths.

### Observability & health
15. **Health checks:** verify a `/health` (or `/api/health`) endpoint exists and reflects app readiness (Nitro server route), and that the orchestrator/load balancer probes it correctly (liveness vs readiness).
16. **Logging/tracing:** flag missing structured logging in production, missing request correlation IDs, no request tracing across the Nuxt→backend boundary (`Cross-Layer` with backend observability skills).
17. **Error observability:** verify errors are captured (not just logged to stdout) and that error responses don't leak internals.

## Detection Logic

1. Inventory deployment artifacts: `Dockerfile*`, `.dockerignore`, `nuxt.config.ts` (nitro/preset), Nginx config, CDN/edge config, CI deploy steps, `.env*`.
2. Capture effective headers/cache behavior on a deployed instance if available (`curl -sI`).
3. Check Node version in Docker/CI vs Nuxt requirements.
4. Verify env var usage: which `NUXT_*`/`runtimeConfig` keys exist, which are required, which are public.

## Evidence Requirements

- Config excerpts with file/line for each finding.
- Captured response headers (security, cache) where a deployed instance is available.
- Docker build/run evidence (base image, user, installed deps).

## Severity

- CRITICAL: committed production secrets; public caching of authenticated responses; HTTP-only production.
- HIGH: missing CSP/security headers on user-content apps, root containers, unsupported Node runtime, missing health checks in orchestrator.
- MEDIUM: missing compression, wrong preset/hosting match, missing cache purge, weak proxy timeouts.
- LOW/INFO: minor Docker layer/ordering notes, header-tuning suggestions.

## False Positives

- Some security headers are intentionally omitted (COEP/COOP trade-offs) — evaluate architecture.
- CDN-set headers may not exist in source config — verify effective headers before reporting missing ones.
- A missing `/health` in a serverless/edge deployment may be legitimate (platform handles health) — evaluate by hosting model.

## Remediation

- Harden the Dockerfile (pinned digest base, non-root, multi-stage, `npm ci --omit=dev`).
- Configure headers/caching at the correct layer (edge/CDN vs Nitro middleware).
- Add env validation with fail-fast; move secrets to the secret store.
- Add health checks and structured logging with correlation IDs.

## Validation

Re-capture headers/cache behavior after fixes; verify health probe passes; verify image builds and runs as non-root; verify the caching chain on authenticated vs public routes.

## Cross-Layer Considerations

- CDN/proxy caching interacts with backend cache headers and SSR route rules — coordinate findings across `5-ssr.md`, `2-security.md`, and backend infra skills (`backend/audit/nestjs/7-cicd-infrastructure.md`, `backend/audit/mezzio/mezzio-cicd-infrastructure-audit.md`).
- Correlation IDs should flow through the Nuxt→backend boundary — align with backend observability skills.
- CORS policy lives at the backend — `Cross-Layer` with backend security skills.

## References

- https://nitro.build/deploy (presets), https://nuxt.com/docs/4.x/recipes/caching
- https://owasp.org/www-project-secure-headers/ (header guidance), https://web.dev/articles/compression

## Report & Log Integration

Progressive persistence to `reports/YYYY-MM-DD/nuxt-07-infrastructure-review.md`; shared log block; shared finding schema.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** Editorial context; the executing model MUST ignore this section.

Nuxt's multi-preset deployment model is the biggest infrastructure-specific trap (wrong preset → broken dynamic routes), so preset/hosting matching is a first-class rule. Header and caching rules deliberately mirror `2-security.md`/`5-ssr.md` but from the "effective edge" perspective — the skill's discipline of verifying effective headers rather than source config avoids the classic infra-audit false positive of reporting a header that the CDN actually sets.
