# Nuxt Security Audit Skill

> **Frontend Audit Suite — Enhanced for DeepSeek-V4 Flash.** Stepwise, deterministic, schema-driven.

## Purpose

Detect security vulnerabilities in a Nuxt application across three distinct trust boundaries: **client-side code** (browser bundle), **server-side Nuxt/Nitro code** (`server/`), and the **backend APIs** it consumes. Every finding must identify which boundary it belongs to and whether the input is attacker-controlled.

## Scope

XSS (incl. DOM XSS), `v-html`, unsafe HTML/URLs, JavaScript URLs, dynamic scripts/styles, SVG injection, iframe/postMessage, CSRF, CORS, authentication/authorization, cookies, JWT/OAuth/OIDC/refresh tokens, token storage, sessions, route middleware, server middleware, Nuxt server routes, Nitro endpoints, SSR security, SSR data leakage, secret exposure (`runtimeConfig`, `runtimeConfig.public`, `NUXT_`, env vars, API keys, internal service URLs), SSRF, path traversal, command injection, request forgery, error leakage, security headers/CSP, dependency & supply-chain risks (delegated to `9-dependencies.md`).

## Framework Context

Nuxt 4 (Vue 3.5, Nitro, Vite). Key surfaces: `app.vue`, `pages/`, `middleware/`, `plugins/`, `composables/`, `components/`, `server/api/`, `server/routes/`, `server/middleware/`, `server/plugins/`, `nuxt.config.ts` (`runtimeConfig`, `routeRules`, `app.head`, modules incl. `@nuxtjs/security`), `.env*`. Nuxt does **not** automatically apply a Content-Security-Policy — headers must be configured (via Nitro/`@nuxtjs/security`/a reverse proxy).

**Version verification (MANDATORY):** confirm the installed Nuxt version first (see `1-audit.md`); Nuxt 3 is EOL.

## Preconditions

1. Repository read access; record commit hash.
2. Reports at `reports/YYYY-MM-DD/nuxt-02-security-review.md`; shared log initialized.
3. Confirm the app is Nuxt (nuxt.config.ts / `nuxt` dependency).

## Audit Objectives

1. Classify every finding by trust boundary (client / server / backend).
2. Identify exploitable vulnerabilities with evidence, not pattern presence.
3. Verify effective security headers on real responses (not just source config).
4. Verify the Nuxt↔backend security contract (auth, CORS, CSRF, tenant isolation) — tag `Cross-Layer`.

## Audit Rules

### Client-side
1. **`v-html`:** flag every use. Trace the bound value: if it can contain attacker-controlled data and there is no sanitization (DOMPurify) and no escaping, report CRITICAL/HIGH. `v-text`/interpolation `{{ }}` escape by default — do not report them (false positive).
2. **Unsafe URLs:** flag `:href`/`:src` bound to user input without scheme allowlisting; `javascript:` URLs from user input are CRITICAL. Verify allowed schemes (`http:`, `https:`, `mailto:` etc.).
3. **Dynamic scripts/styles:** flag `document.createElement('script')`, `new Function`, `eval`, dynamically injected `<style>` with user input, `import()` of user-controlled specifiers (HIGH, code execution).
4. **SVG injection:** flag `v-html`/`innerHTML` rendering of user-supplied SVG (script-bearing) without sanitization.
5. **iframe/postMessage:** flag `window.open` with user input (open redirect), `postMessage` with `targetOrigin: '*'`, and `message` listeners that trust `event.origin` without validation or that use `event.data` in dangerous sinks.
6. **DOM XSS sinks:** flag `innerHTML`, `document.write`, `insertAdjacentHTML`, `outerHTML`, `location` assignment with user input.
7. **Token storage:** token/session in `localStorage`/`sessionStorage` in an XSS-reachable app is HIGH (XSS ⇒ token theft). Prefer `HttpOnly` cookies. `Cross-Layer` with backend auth skill.
8. **CSRF:** for cookie-authenticated apps, verify CSRF protection (SameSite + CSRF token or double-submit). SPA cookie-less bearer flows are less exposed — evaluate by architecture.
9. **Cookie & session hardening:** inspect `set-cookie` behavior (backend-owned, `Cross-Layer`) and client-side cookie reads: flag cookies missing `HttpOnly` for sensitive session/token cookies, missing `Secure`, missing or permissive `SameSite` (`None` on non-HTTPS or without justification), and session cookies readable by client JS where unnecessary. Flag client-side session state (Pinia/`useState`) that mirrors server sessions and can diverge (stale roles, logout without revocation) — MEDIUM/HIGH.
10. **CORS misconfiguration:** client CORS calls are controlled by the BACKEND — frontend findings are `Cross-Layer`; verify the backend allows only intended origins with credentials.

### Server-side (Nuxt/Nitro)
11. **Secret exposure:** any of the following reaching the **client bundle** is CRITICAL: `runtimeConfig` secrets referenced from client code, `NUXT_` env secrets used in `runtimeConfig.public`, API keys, OAuth secrets, DB credentials, signing/encryption keys, internal service URLs. Distinguish: `runtimeConfig` (server-only by default) vs `runtimeConfig.public` (serialized to client). Accessing `useRuntimeConfig().privateKey` inside a composable used by a page can still leak into the bundle — verify the payload sent to the client.
12. **SSR data leakage:** private user data fetched in `useAsyncData`/`useFetch` on a page without `ssr: false` or a suitable route rule is embedded in the initial HTML/payload — HIGH for authenticated pages. Verify what `__NUXT_DATA__`/payload contains.
13. **SSRF / path traversal / command injection:** inspect `server/api/**` and `server/routes/**` handlers: user-controlled URLs passed to `$fetch`/`ofetch`/`fetch` (SSRF), user-controlled paths to `fs`/`readFile` (path traversal), user input to `exec`/`spawn`/`eval` (command injection/RCE — BLOCKER). Validate, allowlist, and pin protocols.
14. **Request forgery / open redirect:** server-side redirects built from user input (`sendRedirect`, `event.node.res` redirects); validate destinations.
15. **Error leakage:** default Nitro error pages/handlers that echo stack traces, internal messages, or headers to clients. Verify `nitro.errorHandler`/`error.vue` and `public` error responses.
16. **Middleware trust:** route middleware (`middleware/`) and server middleware run on the server for SSR — never treat client-side guard logic as authorization. `Cross-Layer`.
17. **CSP/security headers:** evaluate effective headers on real responses: `Content-Security-Policy`, `Strict-Transport-Security`, `X-Content-Type-Options`, `Referrer-Policy`, `Permissions-Policy`, `Cross-Origin-Opener-Policy`, `Cross-Origin-Resource-Policy`, `Cross-Origin-Embedder-Policy`. Do not demand every header blindly — evaluate by architecture (COEP has real costs; CSP nonce/integrity vs inline scripts). Missing CSP on an app rendering user content is HIGH.

### Backend contract
18. AuthN/Z, CSRF, CORS, tenant isolation are `Cross-Layer` findings that reference backend skills (`backend/audit/nestjs/1-security-audit.md`, `backend/audit/mezzio/mezzio-security-vulnerability-audit.md`).

## Detection Logic

1. Extract the client/server boundary: enumerate files under `pages/`, `components/`, `composables/`, `plugins/`, `middleware/`, `app.vue` (client or universal) vs `server/**` (server-only).
2. Grep dangerous sinks: `v-html`, `innerHTML`, `outerHTML`, `document.write`, `eval`, `new Function`, `javascript:`, `postMessage`, `targetOrigin`, `localStorage`, `sessionStorage`, `window.open`, `import(`, `createElement`, `<style>`, SVG usage.
3. For each sink, trace the data flow: source (route param, query, body, store, API response) → transform → sink. Only report when input is attacker-controlled and no sanitization exists.
4. Inspect `nuxt.config.ts` for `runtimeConfig`, `app.head`, headers modules, `routeRules` (private data under caching — see `5-ssr.md`).
5. Inspect `server/**` for dynamic URL/fs/exec usage.
6. Capture effective headers: `curl -sI https://<deployed-app>/` (or local dev server) — compare against `nuxt.config.ts` source config.

## Evidence Requirements

- File + line + code excerpt for every source/sink pair.
- For secrets: the exact line exposing the secret and proof it reaches the client bundle (e.g., a grep of the built client chunk or the serialized `runtimeConfig` payload).
- For headers: the actual captured header block.
- For SSR leakage: the relevant page code + what the payload contains.

## Severity

`BLOCKER | CRITICAL | HIGH | MEDIUM | LOW | INFO` (mapping in `frontend/audit/README.md`). Examples: server-side command injection/RCE, client-bundle secrets, auth bypass ⇒ BLOCKER/CRITICAL; XSS from attacker-controlled input, token in localStorage in XSS-reachable app, private-data SSR leakage ⇒ HIGH; missing CSP/security headers, missing CSRF on cookie-auth, `postMessage` origin wildcard ⇒ MEDIUM; hygiene (deprecated APIs, missing `hreflang`-class items) ⇒ LOW/INFO.

## False Positives

- Interpolation `{{ }}` and `v-text` are escaped — never report.
- `v-html` with a static, trusted string constant is not a vulnerability — report only attacker-controlled data flows.
- `runtimeConfig` values used only server-side are not client exposures.
- `localStorage` token storage in an app with no XSS reachable path is defense-in-depth (MEDIUM/LOW), not CRITICAL — be explicit about the precondition.
- Absence of a header is not automatically a finding — evaluate architecture (e.g., COEP may be consciously omitted).

## Remediation

- `v-html` → escaped output or DOMPurify with a strict config; prefer `v-text`/interpolation.
- Secrets → server-only `runtimeConfig`/Nitro env, mounted secrets; never `runtimeConfig.public`.
- SSR leakage → `ssr: false`/route rules for authenticated pages, or fetch inside `onMounted` with `ssr: false` on the data call; keep private data out of the payload.
- Headers → configure via Nitro server middleware / `@nuxtjs/security` / reverse proxy with a CSP tuned to the app.
- SSRF → URL validation/allowlist, protocol pinning, no attacker-controlled hosts.

## Validation

For each fix, describe the re-check: re-capture headers, re-inspect the client bundle for the secret, re-trace the data flow, run the app's test suite. For the audit: confirm the finding remains reproducible before and after the described fix.

## Cross-Layer Considerations

- Frontend route guard ⇒ backend authorization (never sufficient).
- Client tenant ID ⇒ backend tenant isolation (never a boundary; see multi-tenant rules).
- Token handling ⇒ backend authentication/session skill.
- CORS/CSRF ⇒ backend CORS/CSRF policy.
- API contract ⇒ backend DTO / input-filter validation.
- Caching of authenticated responses ⇒ `5-ssr.md` + `8-infrastructure.md` (public cache of private data is CRITICAL).

## References

- https://nuxt.com/docs/4.x/recipes/security (or current), https://nuxt.com/docs/api/composables/use-runtime-config
- https://github.com/nuxt/security (module), https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy
- OWASP Top 10 / OWASP ASVS (same references as backend security skills)

## Report & Log Integration

Progressive persistence to `reports/YYYY-MM-DD/nuxt-02-security-review.md`; log block in `analysis-log.md` (finding breakdown by severity); findings use the shared schema (ID, Severity, Category, Title, Description, Impact, Affected Files, Evidence, Root Cause, Attack/Failure Scenario, Remediation, Validation, Cross-Layer Impact, References).

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** Editorial context; the executing model MUST ignore this section.

The three-boundary model (client / Nuxt-Nitro server / backend) is the skill's central design choice: Nuxt is the rare frontend framework where the "frontend" includes a real server runtime, so secrets, SSRF, path traversal, and SSR leakage must be audited server-side, while XSS/DOM sinks are audited client-side. The explicit false-positive rules (escaped interpolation, trusted-static `v-html`, server-only runtimeConfig) prevent the two most common over-reporting failure modes in framework audits. Header guidance intentionally avoids "require every header" — an architecture-driven evaluation is a deliberate departure from checklist-style scanners.
