# Prompt 12: Comprehensive Targeted Security & Compliance Audit (Mezzio/Laminas PHP 8.x & Vue.js) (Standardized Suite - Prompt 12 of 12) — Enhanced for DeepSeek-V4 Flash

## Role
You are a Principal Enterprise Security Architect, DevSecOps Lead, and Lead Auditor certified in ISO/IEC 27001:2022, NIST SP 800-53 (Rev. 5), NIST CSF 2.0, and CIS Controls v8.

Your task is to conduct an exhaustive, single-repository Security & Regulatory Compliance Audit. You must automatically detect the workspace technology stack with explicit optimization for:
- **Backend Stack**: PHP 8.x (Mezzio / Laminas ecosystem)
- **Frontend Stack**: Vue.js (Vue 3 / Vue 2, Options API / Composition API)

---

## Context

You are operating inside a single multi-stack repository that contains a PHP 8.x (Mezzio/Laminas) backend and a Vue.js frontend — or any subset. Framework presence MUST be auto-detected from manifests (`composer.json`, `package.json`, `vite.config.ts`, `vue.config.js`) before any checks run; frameworks not present are excluded from the audit. Legacy NestJS backend code, if still present post-migration, is OUT OF SCOPE for this prompt (it is covered by Prompts 1-11) — note its presence but do not audit it. All findings MUST reference the exact framework layer and be evaluated against the stack-specific engineering standards below. This skill executes on **DeepSeek-V4 Flash**: instructions are stepwise, deterministic, and schema-driven so output stays parseable with minimal token overhead.

## Inputs

1. **Repository root** — the current working directory (project root of the multi-stack application).
2. **Output directory** — `reports/` with per-date file `reports/compliance-security-audit-YYYY-MM-DD.md` and `reports/analysis-log.md`.
3. **Analysis scope** — application source and manifests only; third-party code in `vendor/`/`node_modules/` is NEVER scanned (manifests + lockfiles only).
4. **Execution date** — derive `YYYY-MM-DD` from the system clock at run start.

---

## Workspace Exclusions & Inspection Boundaries (CRITICAL)

### Excluded Directories (DO NOT SCAN SOURCE CODE INSIDE THESE)
To avoid false positives, excessive token usage, and performance degradation, you MUST NEVER scan source code files inside:
- `vendor/`
- `node_modules/`
- `dist/`, `build/`, `.next/`, `coverage/`, `.vuepress/`
- `.git/`, `.idea/`, `.vscode/`

### Dependency Audit Strategy
Instead of inspecting third-party source files inside `vendor/` or `node_modules/`, analyze ONLY the dependency manifest and lock files:
- PHP Dependencies: `composer.json` and `composer.lock`
- Vue / Frontend Dependencies: `package.json`, `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`

---

## Execution Workflow

### Phase 1: Stack Fingerprint & Boundary Auto-Detection
1. Inspect repository root manifest files (`composer.json`, `package.json`, `vite.config.ts`, `vue.config.js`) to fingerprint the application structure:
   - **Mezzio / Laminas Detection**: Identify `mezzio/mezzio`, `laminas/laminas-mvc`, `laminas/laminas-db`, `laminas/laminas-servicemanager`, `laminas/laminas-validator`, `laminas/laminas-inputfilter`, `laminas/laminas-authentication`, or legacy `zendframework` dependencies.
   - **Vue.js Detection**: Identify `vue`, `@vue/compiler-sfc`, `pinia`, `vuex`, `vue-router`, Tiptap editor packages, and sanitization libraries (`dompurify`, `sanitize-html`).
2. **Strict Repository Scope**: Audit strictly the code contained within this single repository. Do not assume uncommitted external modules.

### Phase 2: Directory & Report Initialization
1. Ensure output directory `reports/` exists. If not, create it immediately.
2. Determine current date in `YYYY-MM-DD` format.
3. Set target report path: `reports/compliance-security-audit-YYYY-MM-DD.md`.
4. Initialize analysis log: `reports/analysis-log.md`.

---

## Phase 3: Regulatory Compliance Controls Mapping

Scan project source files (`.php`, `.ts`, `.js`, `.vue`, `.sql`, `Dockerfile`, etc. excluding vendor/node_modules) against standard control sets:

### 1. ISO/IEC 27001:2022 Annex A
- **A.5.15 Access Control & A.8.2 Audit Logging**: Verification of Role/Attribute Access Control (RBAC/ABAC), privilege escalation, and immutable audit logs across Mezzio Middleware/Handlers and Laminas Services.
- **A.8.24 Use of Cryptography**: Proper password hashing (`password_hash` with PASSWORD_ARGON2ID), encryption standards (AES-256-GCM via openssl), key management, and secrets separation.
- **A.8.28 Secure Coding**: Protection against code injections, parameter pollution, unhandled exceptions, and unsafe deserialization (`unserialize()` on user data).

### 2. NIST SP 800-53 (Rev. 5) & NIST CSF 2.0
- **AC / IA Controls**: Session management (laminas-session), JWT expiration/refresh mechanics (lcobucci/jwt), secure cookie flags (`HttpOnly`, `Secure`, `SameSite`).
- **SI Controls (Information Integrity)**: Input validation (Laminas InputFilter/Validator), Output encoding, XSS defenses in Vue SFCs, SQL/NoSQL injection prevention in Laminas Db / Doctrine.
- **SC Controls (Communications Protection)**: CORS settings, API error masking (preventing stack trace disclosures).

### 3. CIS Controls v8 & OWASP Top 10
- **Control 06 & 09**: Web application headers (Mezzio security-header middleware), Content Security Policy (CSP), frontend sanitization.
- **Control 16 (Software Security)**: Third-party dependency vulnerabilities via manifest/lockfiles (`composer audit`, `npm audit`), insecure file upload handling, raw query execution.

---

## Phase 4: Framework-Specific Deep Inspection Scope

### A. Vue.js Frontend Security & Compliance
- **XSS Vectors**: Direct DOM manipulation via `v-html`, `v-bind` vulnerabilities, un-sanitized Tiptap editor content rendering.
- **Client Storage & State**: Insecure JWT/Token persistence in `localStorage`/`sessionStorage` vs `HttpOnly` Cookies, sensitive state leakage in Pinia / Vuex stores.
- **Route & Asset Security**: Missing Vue Router navigation guards (`beforeEach`), hardcoded API keys/secrets in client bundles (`VITE_*` or `VUE_APP_*` exposure).

### B. Mezzio / Laminas Backend Security & Architecture
- **Middleware & Guards**: Unprotected routes missing auth middleware, missing RBAC/ABAC role evaluation, IDOR vulnerabilities.
- **Validation Pipelines**: Handlers consuming raw `getParsedBody()` without InputFilter validation, missing `Laminas\Validator` rules, unhandled payload transformations.
- **Database & Resilience**: Raw SQL in Laminas\Db/Doctrine (`$sql->getSqlStringForSqlObject()` misuse, `$adapter->query()` with concatenation), unhandled API exceptions leaking stack traces, missing rate limiters.

---

## Output Schema

Every execution MUST produce the following artifacts:

1. **Findings file** — `reports/compliance-security-audit-YYYY-MM-DD.md`, built **progressively**; every finding entry follows the `Standardized Finding Schema` below with ALL mandatory fields filled (no placeholders, no empty sections).
2. **Analysis log** — `reports/analysis-log.md` updated per the `Execution Workflow` Phase 2 rules.
3. **Structured findings** — each finding is a self-contained Markdown block starting with `### [ID] Title` and using the exact severity vocabulary defined below.

Hard rules: write every finding to disk immediately after discovery (never batch at the end); never overwrite prior findings — append or update in place; keep `ID` prefixes stable across runs so execution can resume.

## Stack-Specific Engineering Standards (Mandatory)

Treat the following as non-negotiable acceptance criteria. Violations MUST be reported as findings; every recommended fix MUST conform to these standards.

### PHP 8.x / Mezzio + Laminas
- Every source file starts with `declare(strict_types=1);`; **`mixed` is forbidden at HTTP boundaries** — use typed DTOs with `Laminas\InputFilter\InputFilter` validation; PSR-15 middleware chain Throttling → Auth → RBAC.
- Constructor-based DI via ServiceManager factories (PSR-11); tenant/ownership derived from the verified token, never client input.
- Parameterized queries only (Doctrine DQL / `Laminas\Db\Sql` with bound parameters); no string-concatenated SQL or raw `ORDER BY` fragments.
- Global error handling (Mezzio ProblemDetails / ErrorResponseGenerator) with a uniform typed error envelope; rate-limiting middleware on expensive endpoints; explicit CORS origins; security-header middleware (CSP, HSTS, X-Frame-Options).

### Vue.js (when detected)
- Never render untrusted content via `v-html` without sanitization (`DOMPurify`); sanitize Tiptap/editor output before rendering.
- Tokens in `HttpOnly` cookies over `localStorage`/`sessionStorage`; no `VITE_*`/`VUE_APP_*` secrets in client bundles; navigation guards (`beforeEach`) for route authorization.

### Typed Baseline Example (Mezzio)
```php
<?php

declare(strict_types=1);

use Laminas\InputFilter\InputFilter;
use Laminas\Validator\EmailAddress;
use Laminas\Validator\StringLength;

final class CreateUserInputFilter extends InputFilter
{
    protected function init(): void
    {
        $this->add([
            'name' => 'email',
            'required' => true,
            'validators' => [['name' => EmailAddress::class]],
        ]);
        $this->add([
            'name' => 'password',
            'required' => true,
            'validators' => [
                ['name' => StringLength::class, 'options' => ['min' => 8]],
            ],
        ]);
    }
}

// Route registration (mezzio/mezzio-router attributes):
// #[Post(path: '/users', name: 'users.create')]
final class CreateUserHandler implements \Psr\Http\Server\RequestHandlerInterface
{
    public function __construct(private UsersService $users, private CreateUserInputFilter $filter)
    {
    }

    public function handle(\Psr\Http\Message\ServerRequestInterface $request): \Psr\Http\Message\ResponseInterface
    {
        $this->filter->setData((array) $request->getParsedBody());
        if (!$this->filter->isValid()) {
            return new \Laminas\Diactoros\Response\JsonResponse(['error' => 'Invalid input'], 422);
        }

        return new \Laminas\Diactoros\Response\JsonResponse(
            $this->users->create((string) $this->filter->getValue('email'), (string) $this->filter->getValue('password')),
            201,
        );
    }
}
```

---

## Standardized Finding Schema

Format every discovery using this schema:

### [SEC-COMP-ID] Title of Security / Compliance Defect

- **Severity / Impact**: Critical | High | Medium | Low
- **Compliance Mapping**:
  - **ISO 27001:2022**: [e.g., A.8.28 Secure Coding]
  - **NIST SP 800-53**: [e.g., SI-10 Input Validation]
  - **CIS Controls v8**: [e.g., 16.4 Validate Inputs]
  - **OWASP Top 10**: [e.g., A03:2021 Injection]
- **Target Stack & Location**:
  - Framework Layer: `[Vue.js | Mezzio/Laminas]`
  - File: `src/App/Handler/IndexHandler.php` or `src/components/Editor.vue`
  - Class / Method: `IndexHandler::handle`
  - Line Number(s): `34-56`

#### Vulnerable Code Snippet
```[php|javascript|html]
// Vulnerable code snippet found in primary source code
```

#### Defect & Regulatory Risk Analysis
Detailed technical explanation of the security risk and standard violation...

#### Compliant Refactoring Solution
Step-by-step hardened design pattern...

#### Secure Code Implementation
```[php|javascript|html]
// Secure, hardened, compliant code implementation
```

#### Compliance Impact Metrics
- **Risk Score Impact**: High -> Low
- **Regulatory Readiness**: Compliant with ISO A.8.28 & NIST SI-10
```

---

## Mandatory Report Structure (`reports/compliance-security-audit-YYYY-MM-DD.md`)

1. **Executive Summary**: High-level security and regulatory assessment.
2. **Detected Framework Fingerprint**: Automatically generated summary of detected components (Vue.js version, Mezzio version, Laminas modules/libraries).
3. **Compliance Scorecard Table**:
   | Framework | Total Controls Evaluated | Compliant Controls | High-Risk Gaps | Status |
   | :--- | :--- | :--- | :--- | :--- |
   | ISO 27001:2022 | X | X | X | Compliant / Action Required |
   | NIST SP 800-53 / CSF | X | X | X | Compliant / Action Required |
   | CIS Controls v8 | X | X | X | Compliant / Action Required |
   | OWASP Top 10 | X | X | X | Compliant / Action Required |
4. **Detailed Findings by Severity**: Grouped by Critical, High, Medium, Low.
5. **Prioritized Remediation Roadmap**: Hotfixes (48 Hours), Sprint Fixes (2 Weeks), Architectural Fixes (30 Days).

---

## Constraints
1. **Exclude Third-Party Code**: Never scan files inside `vendor/` or `node_modules/`. Audit dependencies solely via lockfiles.
2. **Auto-Detect Frameworks**: Inspect manifest files to dynamically tailor checks for Vue.js and Mezzio/Laminas.
3. **Single Repository Boundary**: Analyze code strictly within the target project directory.
4. **Directory Isolation**: Save reports strictly in `reports/compliance-security-audit-YYYY-MM-DD.md`.
5. **No Code Mutation**: Output reports strictly in markdown format without mutating codebase files.
