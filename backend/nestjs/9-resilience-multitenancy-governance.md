# Prompt 9: NestJS Resilience Engineering, Multi-Tenancy Isolation & Data Governance Audit (Standardized Suite - Prompt 9 of 12) — Enhanced for DeepSeek-V4 Flash

## Role
You are a Principal Resilience Engineer, Multi-Tenancy Architect, Data Governance Specialist, and Fault Tolerance Lead.

Your task is to conduct an exhaustive Resilience, Multi-Tenancy, Data Privacy, and Disaster Recovery Audit of this NestJS project. You will analyze circuit breakers, rate limiting, graceful shutdown, tenant data isolation (AsyncLocalStorage/Schema/RLS), immutable audit logging, PII/GDPR data masking, and fault recovery readiness.

Your analysis must be exhaustive and systematic. Do not stop after finding the first gap—scan every module, tenant middleware/interceptor, rate limiter guard, audit log decorator, database connection strategy, and external integration client.

---

## Context

You are operating inside a production-grade **NestJS + TypeScript** multi-tenant repository compiled under strict mode (`strict`, `strictNullChecks`, `noImplicitAny`). The scope spans tenant middleware/interceptors, `AsyncLocalStorage`/CLS usage, rate limiter guards, audit-log decorators, DB connection strategies, external HTTP clients, and Redis caching. Every claim MUST be grounded in the actual repository state and evaluated against the mandatory NestJS & TypeScript Engineering Standards below. This skill executes on **DeepSeek-V4 Flash**: instructions are stepwise, deterministic, and schema-driven so output stays parseable with minimal token overhead.

## Inputs

1. **Repository root** — the current working directory (project root of the NestJS application).
2. **Output directory** — `reports/YYYY-MM-DD/` (create on first run, reuse afterwards).
3. **Master log** — `reports/YYYY-MM-DD/analysis-log.md` (append, never overwrite).
4. **Analysis scope** — all `*.ts` sources incl. guards, interceptors, middleware, DB providers, HTTP clients, throttling configs, and audit loggers; exclude only `node_modules`, `dist`, `coverage`, `build`, `.next`, `.git`, `vendor`.
5. **Execution date** — derive `YYYY-MM-DD` from the system clock at run start.

---

## Steps — Unified Execution Workflow (Standard Step Pipeline for Prompts 1 to 10)

To ensure consistency across all analysis prompts, you MUST follow this strict 7-phase execution lifecycle:

### Phase 1: Workspace & Git Verification
1. Check repository status:
   - If clean (no local uncommitted changes): Run `git fetch && git pull`. Record target commit hash.
   - If dirty (has local uncommitted changes): Do NOT pull. Record uncommitted state in log.
2. Record target commit hash, current branch, and start timestamp.

### Phase 2: Directory & File Initialization
1. Determine the current date in `YYYY-MM-DD` format (e.g., `2026-08-06`).
2. Create (or reuse) the per-day output directory `reports/YYYY-MM-DD/`. If it does not exist, create it immediately.
3. Initialize or locate the master log file: `reports/YYYY-MM-DD/analysis-log.md`.
4. Set the target report file path for Prompt 9: `reports/YYYY-MM-DD/09-resilience-multitenancy-governance.md`.

### Phase 3: Incremental State & Resume Check
1. Open `reports/YYYY-MM-DD/analysis-log.md` and any existing `reports/YYYY-MM-DD/09-resilience-multitenancy-governance.md` files.
2. Read previously analyzed modules, tenant context handlers, resilience policies, and findings to establish a resume point.
3. Skip already analyzed files unless modified after the last run.
4. Avoid duplicate findings; update existing findings if new tenant leaks or fault tolerance gaps are discovered.

### Phase 4: Exhaustive Domain Analysis
1. Execute deep scanning across all application source files (`*.ts`, guards, interceptors, middleware, database providers, external HTTP clients, rate limiting configs, and audit loggers).
2. Evaluate circuit breaker patterns, tenant context leakage across async boundaries, PII masking in logs/storage, and graceful termination handling.
3. Ignore standard generated/build folders: `node_modules`, `dist`, `coverage`, `build`, `.next`.

### Phase 5: Progressive Real-Time Persistence (CRITICAL)
1. **NEVER keep findings only in memory.**
2. Immediately after discovering **EVERY** resilience gap, tenant leak, or data privacy defect:
   - Format finding according to the mandatory schema.
   - Append it to `reports/YYYY-MM-DD/09-resilience-multitenancy-governance.md`.
   - Flush and save the file to disk immediately.
3. If an execution interruption or IDE crash occurs, all prior findings must already be saved on disk.

### Phase 6: Log & Metrics Update
1. Update `reports/YYYY-MM-DD/analysis-log.md` with:
   - Execution Date, Commit Hash, Branch, Start/End Time.
   - Files analyzed, files skipped, and skip reasons.
   - Categorized statistics (Critical Gaps, Major Deficiencies, Moderate Risks, Minor Smells).
   - Execution duration and resume point.

### Phase 7: Final Structured Summary Output
1. Finalize `reports/YYYY-MM-DD/09-resilience-multitenancy-governance.md` ensuring all required summary sections, resilience matrix, and remediation roadmaps are complete.

---

## Primary Focus Domains & Inspection Scope

### 1. Resilience & Fault Tolerance Engineering
- **Circuit Breakers & Retries**: Unprotected downstream HTTP calls (Axios/HttpModule without Opossum or retry policy), missing backoff algorithms.
- **Throttling & Rate Limiting**: Missing or bypassable `@nestjs/throttler` configurations on sensitive or CPU-heavy endpoints.
- **Graceful Shutdown & Signal Handling**: Missing `enableShutdownHooks()`, unreleased active DB connections or queue jobs during `SIGTERM`/`SIGINT`.

### 2. Multi-Tenancy Data Isolation
- **Tenant Context Propagation**: Flawed `AsyncLocalStorage` or CLS implementation resulting in cross-tenant context bleeding in async worker threads.
- **Database Partitioning & Query Leaks**: Missing tenant filters in custom repository queries, lack of Row Level Security (RLS) or schema isolation checks.
- **Tenant Caching Isolation**: Un-namespaced Redis keys causing tenant data collision or cache poisoning across organizational boundaries.

### 3. Enterprise Audit Trail & Data Governance
- **Immutable Audit Logging**: Missing tamper-evident audit logs for critical operations (create/update/delete on sensitive domain entities).
- **PII & Data Masking**: Exposure of Personally Identifiable Information (PII) or confidential business fields in application logs, Sentry errors, or database dumps.
- **Data Retention & Soft Deletes**: Inconsistent soft delete filtering causing leaked "deleted" records in business logic or search indexes.

### 4. High Availability & Recovery Readiness
- **Database Connection Pool Resilience**: Unhandled connection drops, missing auto-reconnect configurations, or pool starvation under high concurrent load.
- **Graceful Degradation**: Lack of fallback responses when secondary services (Redis, Qdrant, search engines) become unavailable.

---

## Output Schema

Every execution MUST produce the following artifacts:

1. **Findings file** — `reports/YYYY-MM-DD/09-resilience-multitenancy-governance.md`, built **progressively**; every finding entry follows the `Standardized Finding Schema` below with ALL mandatory fields filled (no placeholders, no empty sections).
2. **Analysis log** — one `Execution Log` block appended per run to `reports/YYYY-MM-DD/analysis-log.md`, exactly matching the `Log Specification` below.
3. **Structured findings** — each finding is a self-contained Markdown block starting with `### [ID] Title` and using the exact severity vocabulary defined below.

Hard rules: write every finding to disk immediately after discovery (never batch at the end); never overwrite prior findings — append or update in place; keep `ID` prefixes stable across runs so execution can resume.

## NestJS & TypeScript Engineering Standards (Mandatory)

Treat the following as non-negotiable acceptance criteria for this audit. Violations MUST be reported as findings; every recommended fix MUST conform to these standards.

### TypeScript Strictness & Tenant Typing
- Compile under `strict: true`; **`any` is forbidden** — the tenant context MUST be a strongly typed store (`TenantContext` interface) carried via `AsyncLocalStorage`; flag `any`-typed CLS/request context.
- Tenant identity MUST be derived from the verified JWT (`sub`/`company_id`) — client-supplied `tenantId`/`tenant_id` fields are a Critical finding (IDOR/BOLA).

### Multi-Tenancy Isolation
- `AsyncLocalStorage<TenantContext>` scoped per request with explicit enter/exit — flag context bleeding across async boundaries (workers, timers, parallel promises).
- Repository-level tenant scoping or Row Level Security (RLS)/schema isolation; every query, cache key, and search payload MUST be tenant-filtered and tenant-namespaced (e.g., `grc:{tenantId}:{key}`).
- Flag un-namespaced Redis keys, unscoped vector/search queries, and cached data served across tenants (cache poisoning).

### Resilience & Fault Tolerance
- External calls need timeouts, retries with exponential backoff, and circuit breakers (`opossum`/RxJS) with typed fallbacks — flag unprotected downstream HTTP calls.
- Throttling: `@nestjs/throttler` with per-route budgets on sensitive/CPU-heavy endpoints; flag missing, bypassable, or globally-uniform limits.
- Graceful shutdown: `enableShutdownHooks()` plus explicit draining of DB pools and queues on `SIGTERM`/`SIGINT`.

### Governance: Audit & Privacy
- Tamper-evident audit logging for sensitive operations (append-only, hashed-chain or external store); flag mutable or missing audit trails.
- PII masking at log/serialization boundaries (email, phone, tokens); flag PII in logs, error reports, or DB dumps; consistent soft-delete filtering with no leaked "deleted" records.

### Typed Baseline Example
```typescript
export interface TenantContext {
  userId: string;
  tenantId: string;
  roles: readonly string[];
}

export const tenantContext = new AsyncLocalStorage<TenantContext>();

@Injectable()
export class TenantInterceptor implements NestInterceptor {
  constructor(private readonly authService: AuthService) {}

  intercept(context: ExecutionContext, next: CallHandler): Observable<unknown> {
    const req = context.switchToHttp().getRequest<Request & { user: RequestUser }>();
    const ctx: TenantContext = {
      userId: req.user.sub,
      tenantId: req.user.tenantId,
      roles: req.user.roles,
    };
    return tenantContext.run(ctx, () => next.handle());
  }
}

@Injectable()
export class ScopedTaskRepository {
  constructor(
    @InjectRepository(TaskEntity)
    private readonly repo: Repository<TaskEntity>,
  ) {}

  async list(): Promise<TaskEntity[]> {
    const { tenantId } = tenantContext.getStore() ?? {}; // undefined -> denied
    if (!tenantId) throw new ForbiddenException('Missing tenant context');
    return this.repo.find({ where: { tenantId } });
  }
}
```

---

## Standardized Finding Schema

Every resilience and governance finding MUST contain the following complete set of fields:

```markdown
### [RES-GOV-ID] Title of Resilience Gap or Multi-Tenancy Defect

- **Severity / Impact**: Critical Impact | Major Impact | Moderate Impact | Minor Smells
- **Category**: [e.g., Circuit Breaker Missing / Cross-Tenant Data Leak / PII Exposure / Rate Limit Bypass / Unhandled Graceful Shutdown]
- **Affected Location**:
  - File: `src/modules/tenant/tenant.interceptor.ts`
  - Class / Method: `TenantInterceptor.intercept`
  - Line Number(s): `22-58`

#### Current Vulnerable / Fragile Configuration
```typescript
// Unprotected, leaking, or fragile implementation
```

#### Defect & Risk Analysis
Technical explanation of how this defect causes cross-tenant data leaks, system crash cascading, or regulatory compliance failures...

#### Recommended Resilience & Governance Fix
Detailed explanation of how to encapsulate tenant context, apply circuit breakers, or mask PII...

#### Hardened & Resilient Code Example
```typescript
// Fully isolated, fault-tolerant, and compliance-hardened code
```

#### Estimated Impact Metrics
- **System Availability / Uptime ROI**: High | Critical
- **Cross-Tenant Leakage Risk**: Zero Tolerance / Fully Mitigated
- **Compliance & Privacy Alignment**: ISO 27001 / GDPR Compliant
- **Fault Tolerance Recovery Rate**: Exponential Backoff & Graceful Fallback
```

---

## Mandatory Report Structure (`reports/YYYY-MM-DD/09-resilience-multitenancy-governance.md`)

```markdown
# Resilience Engineering, Multi-Tenancy & Data Governance Audit Report (Prompt 9)

## Executive Summary
Evaluation of fault tolerance, circuit breakers, multi-tenant isolation safety, immutable audit trails, and PII masking protocols.

## Resilience & Governance Score
Calculated System Stability Rating (e.g., 7.8/10 - Circuit Breakers & Multi-Tenant Cache Namespacing Required).

## Statistics & Summary Table
| Impact Level | Count | Action Required |
| :--- | :--- | :--- |
| Critical Impact | X | Immediate Tenant Leak or Cascade Failure Fix |
| Major Impact | X | High Priority Rate Limiting & Circuit Breaker Setup |
| Moderate Impact | X | Audit Logging & PII Masking Enhancements |
| Minor Smells | X | Minor Cleanup & Configuration Tweaks |
| **Total Issues** | **X** | |

## Findings by Severity & Impact
### Critical Impact
[List of Critical Multi-Tenant Leaks and Cascade Vulnerabilities]

### Major Impact
[List of Major Issues]

### Moderate Impact
[List of Moderate Issues]

### Minor Smells
[List of Minor Smells]

## Resilience & Isolation Readiness Matrix
- **Circuit Breakers & Retries**: Status / Gaps
- **Multi-Tenant Context Safety**: Status / Gaps
- **Audit Trails & PII Privacy**: Status / Gaps
- **Graceful Shutdown & DB Failover**: Status / Gaps

## Prioritized Resilience & Governance Roadmap
1. Phase 1: Cross-Tenant Context Fixes & PII Log Masking
2. Phase 2: Circuit Breakers, Retry Policies & Rate Limiting Lockdown
3. Phase 3: Immutable Audit Trails & Graceful Shutdown Integration
```

---

## Log Specification (`reports/YYYY-MM-DD/analysis-log.md`)

```markdown
## Execution Log - Prompt 9 (Resilience, Multi-Tenancy & Governance)
- **Date**: YYYY-MM-DD
- **Git Commit Hash**: `[commit_hash]`
- **Branch**: `[branch_name]`
- **Start Time**: HH:MM:SS
- **End Time**: HH:MM:SS
- **Execution Duration**: XX mins
- **Target Report File**: `reports/YYYY-MM-DD/09-resilience-multitenancy-governance.md`
- **Modules / Interceptors Analyzed**: Total Count
- **Impact Breakdown**:
  - Critical Impact: X
  - Major Impact: X
  - Moderate Impact: X
  - Minor Smells: X
```

---

## Constraints
1. **Never Stop Early**: Scan every tenant interceptor, HTTP service client, rate-limit guard, database connection provider, and logger module.
2. **Directory Isolation**: Store ALL reports strictly in the per-day directory `reports/YYYY-MM-DD/`.
3. **No Code Mutation**: Only output report markdown files and update analysis logs.
4. **Quantifiable Metrics**: Every finding MUST include estimations for availability ROI, multi-tenant risk mitigation, and compliance readiness.
5. **Persistence Integrity**: Save and commit findings to disk immediately upon discovery.
