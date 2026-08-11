# Vue Security Audit Skill

> **Frontend Audit Suite — Enhanced for DeepSeek-V4 Flash.** Stepwise, deterministic, schema-driven.

## Purpose

Detect security vulnerabilities in a Vue application with a precise understanding of Vue's security model: default interpolation escaping is safe, and only explicit sinks (`v-html`, dynamic attributes, render functions, DOM manipulation) create XSS surface. Every finding must trace attacker-controlled input to a reachable sink.

## Scope

XSS, `v-html`, unsafe HTML, unsafe URLs, DOM manipulation, dynamic attributes, dynamic components, render functions, JSX, SVG, iframe, postMessage, third-party content, Markdown, rich text, authentication, authorization, route guards, token storage, cookies, CSRF, CORS, secrets, environment variables, dependency security (delegated to `8-dependencies.md`).

## Framework Context

Vue 3.5: interpolation `{{ }}` and `v-text` escape by default; `v-html` does NOT escape (documented by the framework); dynamic attributes (`:href`, `:src`, `:style`) can be injection points; render functions/h (hyperscript) and JSX bypass template escaping; `v-bind` object spread and dynamic component `:is` need care. CSP: Vue's runtime does not use `eval` by default (compiled templates), but inline styles/components may require nonces.

**Version verification (MANDATORY):** see `1-audit.md`. Vue 2 (EOL) changes some APIs (filters, `$scopedSlots`) — adjust checks.

## Preconditions

1. Repository read access; record commit hash.
2. Reports at `reports/YYYY-MM-DD/vue-02-security-review.md`; shared log initialized.
3. Confirm the app is Vue (`vue` dependency, `App.vue`/`main.ts`).

## Audit Objectives

1. Identify XSS sinks with attacker-controlled data flows.
2. Verify token/session storage and authentication wiring (`Cross-Layer` with backend).
3. Verify CSRF/CORS posture against backend policy.
4. Verify secrets/env handling (no secrets in client bundles).
5. Verify third-party content rendering (Markdown/rich text/editor output) is sanitized.

## Audit Rules

### XSS & injection sinks
1. **`v-html`:** flag every use. Trace the bound value: attacker-controlled + no sanitization (DOMPurify) ⇒ HIGH/CRITICAL. Static trusted strings are not findings (false positive).
2. **Unsafe URLs:** flag `:href`/`:src` from user input without scheme allowlisting; `javascript:` URLs from user input are CRITICAL. Allow only `http:`, `https:`, `mailto:` etc. as appropriate.
3. **Dynamic attributes/styles:** flag `:style` with user input (CSS injection/UI redress), `:class` from user input is generally safe (string/array), `v-bind` object spread onto elements from user data (attribute injection) — flag with data-flow evidence.
4. **Dynamic components:** flag `:is` bound to user-controlled component names (component injection if names resolve from user data) — HIGH; validate against an allowlist.
5. **Render functions & JSX:** flag `h()`/JSX interpolating user input without escaping (render functions do not auto-escape) — HIGH. `h('div', userHtml)` is an innerHTML-equivalent sink.
6. **DOM manipulation:** flag `innerHTML`, `document.write`, `insertAdjacentHTML`, `outerHTML`, `eval`, `new Function`, `document.createElement` with user strings — HIGH/CRITICAL where reachable.
7. **SVG injection:** flag `v-html`/`innerHTML` with user-supplied SVG (script-bearing) without sanitization.
8. **iframe/postMessage:** flag `window.open` with user input (open redirect), `postMessage` with `targetOrigin: '*'`, and `message` handlers that don't validate `event.origin` or that use `event.data` in dangerous sinks.
9. **Markdown/rich text:** flag rendering of user Markdown/rich-text content (Tiptap, Markdown-it, editor output) without sanitization at the render boundary (DOMPurify) — HIGH for user-generated content.

### Auth & session
10. **Token storage:** token in `localStorage`/`sessionStorage` in an XSS-reachable app is HIGH (XSS ⇒ token theft); prefer `HttpOnly` cookies. `Cross-Layer` with backend auth skill.
11. **Route guards:** router guards (`beforeEach`, `meta.requiresAuth`) are navigation UX — never authorization. Missing guard on a private route is MEDIUM (UX/data-exposure aid), but authorization enforcement is backend's job (`Cross-Layer` mandatory).
12. **Auth state handling:** flag client-only auth state that diverges from the server session (logout doesn't revoke, stale roles) — MEDIUM/HIGH.
13. **Cookie & session hardening:** inspect how session/auth cookies are set (backend-owned, `Cross-Layer`) and how client code reads them: flag sensitive cookies without `HttpOnly`, missing `Secure`, permissive or missing `SameSite`, and any client-side JS reading session cookies unnecessarily. Flag refresh-token handling that exposes long-lived tokens to JS — MEDIUM/HIGH.
14. **CSRF:** for cookie-authenticated apps verify CSRF protection (SameSite + token or backend double-submit). Evaluate by architecture; bearer-token apps are less exposed.
15. **CORS:** frontend CORS usage is controlled by the backend — `Cross-Layer`; verify backend allows only intended origins with credentials.

### Secrets & env
16. **Secrets in client bundle:** any `VITE_*`/`VUE_APP_*` secret, API key, or private URL exposed to the client bundle is CRITICAL if it should be server-only; distinguish public client config from secrets. Verify `.env` files aren't committed (CRITICAL).
17. **Hardcoded secrets:** flag hardcoded API keys/tokens in source — CRITICAL/HIGH.
18. **Error leakage:** flag error UI exposing raw backend messages/stack traces — MEDIUM (`Cross-Layer`).

### Dependency security
19. Delegate to `8-dependencies.md`; flag known-vulnerable packages as findings there (HIGH/CRITICAL per advisory).

## Detection Logic

1. Grep sinks: `v-html`, `innerHTML`, `outerHTML`, `document.write`, `eval(`, `new Function`, `javascript:`, `postMessage`, `targetOrigin`, `localStorage`, `sessionStorage`, `window.open`, `h(`/JSX files, `:is=`.
2. For each sink, trace: source (route param/query, API response, store, user input) → transform → sink. Report only attacker-controlled, unreachable-mitigated flows.
3. Inspect auth wiring: token storage location, router guards, auth store, fetch/axios interceptors.
4. Inspect `.env*` and `vite.config` for exposed secrets and committed env files.
5. Check third-party content rendering (editor components, markdown renderers) for sanitization.

## Evidence Requirements

- File + line + code excerpt for each source/sink pair.
- Token storage: the exact line and why XSS reaches it.
- Secret exposure: the env/constant line and proof it enters the client bundle (built chunk grep).
- Behavior evidence where relevant (e.g., rendered HTML with injected markup).

## Severity

- BLOCKER/CRITICAL: committed secrets, `javascript:` URL injection, RCE-adjacent sinks reachable from user input.
- HIGH: XSS from attacker-controlled `v-html`/render functions, token in localStorage in XSS-reachable app, un-sanitized rich-text rendering.
- MEDIUM: missing router guard on private route, CSRF posture gaps on cookie-auth, error leakage, unvalidated `postMessage` origin.
- LOW/INFO: minor hygiene, missing scheme allowlist on internal links.

## False Positives

- **Vue's default escaping is safe** — `{{ }}`, `v-text`, `:title`, `:alt` are escaped; never report them.
- `v-html` with a static constant is not a vulnerability.
- `:class`/`:id` bound to strings are generally safe — only dynamic attributes with URLs/styles/event handlers are sinks.
- `localStorage` token in an app with no XSS-reachable path is defense-in-depth (MEDIUM/LOW), not CRITICAL — state the precondition.
- Router guards missing on public routes are not findings.

## Remediation

- `v-html` → escaped output or DOMPurify with strict config; prefer rendering via components.
- Render functions/JSX → escape user data explicitly (e.g., `escapeHtml` helper) or use escaped interpolation.
- Tokens → HttpOnly cookies (backend change, `Cross-Layer`); otherwise mitigate XSS surface.
- Secrets → server-side env / backend proxy; never `VITE_*` for secrets.
- Rich text → sanitize at render boundary (DOMPurify), never rely on editor output alone.

## Validation

For each fix, describe the re-check: re-trace the data flow, re-render the injection payload, verify the token is no longer JS-readable, re-check the built bundle for secrets.

## Cross-Layer Considerations

- Route guard ⇒ backend authorization (never sufficient).
- Tenant ID handling ⇒ backend tenant isolation (never a boundary).
- Token handling ⇒ backend authentication/session.
- CORS/CSRF ⇒ backend CORS/CSRF policy.
- API contract ⇒ backend DTO/input-filter validation.

## References

- https://vuejs.org/guide/essentials/template-syntax (escaping), https://vuejs.org/guide/best-practices/security (official security guide)
- https://github.com/cure53/DOMPurify, OWASP XSS Prevention Cheat Sheet

## Report & Log Integration

Progressive persistence to `reports/YYYY-MM-DD/vue-02-security-review.md`; shared log block; shared finding schema.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** Editorial context; the executing model MUST ignore this section.

Vue's security model is the skill's backbone: the escape-by-default doctrine is stated up front so the auditor never flags safe interpolation, and every rule clusters around the real Vue sink set (`v-html`, dynamic attributes, render functions/JSX, dynamic components). The `:is`-component-injection and render-function escaping rules are the least-known Vue XSS vectors, so they are explicitly enumerated; the rich-text/editor rule reflects that most real Vue XSS incidents come from un-sanitized user-generated content.
