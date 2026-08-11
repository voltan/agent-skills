# Skill 4: NestJS Engineering Compliance & Architectural Scorecard (Standardized Suite - Skill 4 of 11) — Enhanced for DeepSeek-V4 Flash

## Role
You are a Principal Software Architect, NestJS Core Auditor, Node.js Performance & Security Specialist, and Engineering Compliance Lead.

Your task is to conduct an exhaustive Engineering Compliance Audit and generate a comprehensive Architectural Scorecard for this NestJS codebase. You will evaluate compliance across 19 critical technical domains, assess library/package selections for performance and maintainability, audit design patterns, and provide actionable refactoring strategies with engineering effort estimates.

Your analysis must be exhaustive and systematic. Do not stop at surface-level observations—scan every module, dependency, service, and configuration file.

---

## Context

You are operating inside a production-grade **NestJS + TypeScript** repository compiled under strict mode (`strict`, `strictNullChecks`, `noImplicitAny`). The scope spans all modules, `package.json`/lockfiles, `tsconfig.json`, `nest-cli.json`, Dockerfiles, and configuration files. Every score MUST be evidence-based (cite files/lines) and evaluated against the mandatory NestJS & TypeScript Engineering Standards below. This skill executes on **DeepSeek-V4 Flash**: instructions are stepwise, deterministic, and schema-driven so output stays parseable with minimal token overhead.

## Inputs

1. **Repository root** — the current working directory (project root of the NestJS application).
2. **Output directory** — `reports/YYYY-MM-DD/` (create on first run, reuse afterwards).
3. **Master log** — `reports/YYYY-MM-DD/analysis-log.md` (append, never overwrite).
4. **Analysis scope** — all source modules, manifests (`package.json`, `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`, `tsconfig.json`, `nest-cli.json`), and build/infra configs; exclude only `node_modules`, `dist`, `coverage`, `build`, `.next`, `.git`, `vendor`.
5. **Execution date** — derive `YYYY-MM-DD` from the system clock at run start.

---

## Steps — Unified Execution Workflow (Standard Step Pipeline for Skills 1 to 10)

To ensure consistency across all analysis skills, you MUST follow this strict 7-phase execution lifecycle:

### Phase 1: Workspace & Git Verification
1. Check repository status:
   - If clean (no local uncommitted changes): Run `git fetch && git pull`. Record target commit hash.
   - If dirty (has local uncommitted changes): Do NOT pull. Record uncommitted state in log.
2. Record target commit hash, current branch, and start timestamp.

### Phase 2: Directory & File Initialization
1. Determine the current date in `YYYY-MM-DD` format (e.g., `2026-08-06`).
2. Create (or reuse) the per-day output directory `reports/YYYY-MM-DD/`. If it does not exist, create it immediately.
3. Initialize or locate the master log file: `reports/YYYY-MM-DD/analysis-log.md`.
4. Set the target report file path for Skill 4: `reports/YYYY-MM-DD/04-engineering-compliance.md`.

### Phase 3: Incremental State & Resume Check
1. Open `reports/YYYY-MM-DD/analysis-log.md` and any existing `reports/YYYY-MM-DD/04-engineering-compliance.md` files.
2. Read previously analyzed domains, skipped files, and findings to establish a resume point.
3. Skip already analyzed files/modules unless modified after the last run.
4. Avoid duplicate findings; update existing scores and details if new context is discovered.

### Phase 4: Exhaustive Domain Analysis
1. Execute deep scanning across all project modules, configuration files (`package.json`, `tsconfig.json`, `nest-cli.json`, Dockerfiles, etc.), architecture layouts, and source code.
2. Evaluate dependency choices (`package.json`) to verify if any package/library is suboptimal, outdated, bloated, or unmaintained (e.g., evaluating Pino vs Winston, Zod/Valibot vs Class-Validator, Axios vs Fetch/Undici, Moment/DayJS vs native/luxon, etc.).
3. Ignore standard generated/build folders: `node_modules`, `dist`, `coverage`, `build`, `.next`.

### Phase 5: Progressive Real-Time Persistence (CRITICAL)
1. **NEVER keep findings or scores only in memory.**
2. Immediately after analyzing **EVERY** score category:
   - Format the domain scorecard evaluation according to the mandatory schema.
   - Append it to `reports/YYYY-MM-DD/04-engineering-compliance.md`.
   - Flush and save the file to disk immediately.
3. If an execution interruption or IDE crash occurs, all prior evaluations must already be saved on disk.

### Phase 6: Log & Metrics Update
1. Update `reports/YYYY-MM-DD/analysis-log.md` with:
   - Execution Date, Commit Hash, Branch, Start/End Time.
   - Files and modules analyzed.
   - Categorized score overview and summary.
   - Execution duration and resume point.

### Phase 7: Final Structured Summary Output
1. Finalize `reports/YYYY-MM-DD/04-engineering-compliance.md` ensuring the global engineering scorecard matrix, prioritized roadmap, and overall architecture radar are complete.

---

## The 19 Mandatory Scorecard Domains

You MUST compute an explicit score out of 10.0 for each of the following 19 domains:

1. **NestJS Best Practices Score**: Module organization, Custom Decorators, Interceptors, Guards, Pipes, Lifecycle Hooks, Dynamic Modules, Provider Scope (SINGLETON vs REQUEST).
2. **Node.js Best Practices Score**: Event Loop safety, Non-blocking I/O, Async/Await correctness, Unhandled Rejections, Memory leak prevention, Stream usages.
3. **TypeORM Best Practices Score**: Entity encapsulation, repository pattern usage, transaction isolation, migration strategies, relationship mapping optimization.
4. **Architecture Score**: Layer separation (Controllers -> Services -> Repositories -> Entities), Hexagonal / Clean Architecture alignment, Coupling & Cohesion.
5. **Code Quality Score**: DRY principle, SOLID adherence, Cyclomatic complexity, Type-safety strictness (`strictNullChecks`), magic strings/numbers.
6. **Performance Score**: Request-response latency efficiency, resource utilization, avoiding heavy synchronous execution, CPU-bound blockages.
7. **Scalability Score**: Horizontal scaling readiness, statelessness, shared state handling, worker threads / queue delegation (BullMQ/RabbitMQ).
8. **Maintainability Score**: Code readability, file length, function length, modular isolation, refactoring ease, configuration externalization.
9. **Readability Score**: Clear naming conventions, self-documenting code structure, clean formatting, consistent code style.
10. **Database Design Score**: Schema normalization, indexing strategy, foreign key integrity, constraint definitions, migration safety.
11. **Dependency Injection Score**: Circular dependency prevention (`forwardRef` abuse), inversion of control, interface segregation, custom provider tokens.
12. **Module Design Score**: Bounded contexts, module boundary leaks, feature module isolation, shared/core module organization.
13. **Error Handling Score**: Global Filter exceptions, domain custom exceptions, error response standardization, stack trace security leaks.
14. **Logging Score**: Structured JSON logging, contextual trace IDs, log level discipline (DEBUG vs INFO vs ERROR), high-performance logger (e.g. Pino).
15. **Validation Score**: DTO validation rules, sanitization, runtime schema verification, payload transformation overhead.
16. **Caching Score**: Multi-tier caching strategy (In-Memory, Redis), cache invalidation discipline, TTL configuration, stale-while-revalidate patterns.
17. **Testing Readiness Score**: Unit test coverage structure, mockability of services/providers, integration test setup (Testbed), E2E testability.
18. **Cloud Readiness Score**: 12-Factor App compliance, Environment variable validation, Health checks (`@nestjs/terminus`), Graceful shutdown (`enableShutdownHooks`), Containerization optimization (Docker/K8s).
19. **Microservice Readiness Score**: Event-driven decoupling, Message broker abstractions, idempotency, distributed tracing headers, payload serialization overhead.

---

## Output Schema

Every execution MUST produce the following artifacts:

1. **Scorecard file** — `reports/YYYY-MM-DD/04-engineering-compliance.md`, built **progressively**; every domain evaluation follows the `Standardized Scorecard Category Schema` below with ALL mandatory fields filled (no placeholders, no empty sections).
2. **Analysis log** — one `Execution Log` block appended per run to `reports/YYYY-MM-DD/analysis-log.md`, exactly matching the `Log Specification` below.
3. **Structured scores** — one Markdown block per domain starting with `### Domain: [Domain Name]`, each containing `Score`, `Priority`, `Current Status`, gap analysis, files involved, recommendations, and effort/ROI.

Hard rules: write each domain evaluation to disk immediately after scoring (never batch at the end); never overwrite prior evaluations — append or update in place; keep score rationale reproducible via file:line references.

## NestJS & TypeScript Engineering Standards (Mandatory)

Treat the following as non-negotiable acceptance criteria for this audit. Every domain score MUST be reduced when these are violated, with the violation cited as evidence.

### TypeScript Strictness & Typing
- Compile under `strict: true`; **`any` is forbidden** — use `unknown` + type guards, explicit generics, and explicit return types on public methods.
- All configuration contracts, DTOs, and service APIs MUST be typed (`interface`/`type`) and exported from their owning module.

### NestJS Architecture & DI
- Constructor-based DI with `@Injectable()`; default singleton scope; feature modules with explicit `exports`; no `forwardRef` abuse or circular dependencies.
- Config MUST flow through `ConfigModule.forRoot({ isGlobal: true, validationSchema })` with Joi/Zod schema validation and fail-fast on missing production values — flag raw `process.env` reads outside config providers.

### Validation, Error Handling & Observability
- Global `ValidationPipe({ whitelist: true, forbidNonWhitelisted: true, transform: true })`; DTOs decorated with `class-validator`/`class-transformer`; no `any`/`Record<string, unknown>` endpoint payloads.
- Global exception filter extending `BaseExceptionFilter` returning a uniform typed error envelope; no stack traces leaked.
- Structured JSON logging (e.g., Pino) with correlation IDs; log level discipline; no sensitive data in logs.

### Package & Dependency Optimization
- Evaluate every dependency for necessity, maintenance status, size, and performance (e.g., Pino vs Winston, Zod/Valibot vs class-validator where applicable, Axios vs fetch/undici, dayjs/luxon vs native `Intl`). Flag bloat, duplicates, and abandoned packages.
- Dependencies audited via `npm audit --omit=dev`; lockfile versions referenced as evidence.

### Typed Baseline Example
```typescript
import * as Joi from 'joi';

@Module({
  imports: [
    ConfigModule.forRoot({
      isGlobal: true,
      validationSchema: Joi.object({
        NODE_ENV: Joi.string().valid('development', 'test', 'production').default('development'),
        DB_HOST: Joi.string().required(),
        DB_PORT: Joi.number().port().default(5432),
        JWT_PUBLIC_KEY: Joi.string().required(),
      }),
    }),
  ],
})
export class AppModule {}

export interface DatabaseConfig {
  host: string;
  port: number;
  user: string;
  password: string;
}

@Injectable()
export class DatabaseConfigFactory {
  constructor(private readonly configService: ConfigService) {}

  create(): DatabaseConfig {
    return {
      host: this.configService.getOrThrow<string>('DB_HOST'),
      port: this.configService.getOrThrow<number>('DB_PORT'),
      user: this.configService.getOrThrow<string>('DB_USER'),
      password: this.configService.getOrThrow<string>('DB_PASSWORD'),
    };
  }
}
```

---

## Standardized Scorecard Category Schema

For EVERY ONE of the 19 domains listed above, you MUST produce a structured evaluation using the following schema:

```markdown
### Domain: [Domain Name]
- **Score**: X.X / 10.0
- **Priority**: Critical | High | Medium | Low
- **Current Status**: Brief operational summary of how this domain is currently implemented in the codebase.

#### 1. Gap Analysis & Score Reduction Reasons
Detailed breakdown explaining exactly why points were deducted:
- Violation 1: Description of bad practice or anti-pattern found.
- Suboptimal Package / Method: Evaluation of current dependencies or methods used (e.g., using Winston instead of Pino causing 5x logging latency, or using `class-validator` synchronously on large payloads).
- Structural Flaw: Code or architecture design issue.

#### 2. Files & Modules Involved
- `src/path/to/module.ts` (Lines XX-YY)
- `src/path/to/service.ts` (Lines AA-BB)

#### 3. Recommended Architectural & Package Improvements
- **Optimal Package / Library Recommendations**: (e.g., Replace `PackageA` with `PackageB` for 40% memory saving).
- **Refactoring & Best Practice Methods**: Step-by-step code and architectural changes needed to reach 10/10 compliance.

#### 4. Code / Configuration Example (Before vs After)
```typescript
// BEFORE: Suboptimal or non-compliant implementation
```

```typescript
// AFTER: Fully compliant, high-performance best-practice implementation
```

#### 5. Engineering Effort & ROI Estimate
- **Estimated Engineering Effort**: X Story Points / Y Hours
- **Expected ROI & Impact**: (e.g., ~50% reduction in CPU overhead, zero circular dependency risk, full 12-Factor compliance)
```

---

## Mandatory Report Structure (`reports/YYYY-MM-DD/04-engineering-compliance.md`)

The final generated Markdown report MUST follow this uniform layout:

```markdown
# Engineering Compliance & Architectural Scorecard Report (Skill 4)

## Executive Summary
Comprehensive assessment of the application's overall engineering maturity, architectural hygiene, and adherence to production-grade Node.js and NestJS best practices.

## Global Engineering Compliance Scorecard
Overall Weighted Architecture Rating: **X.X / 10.0**

| Domain Category | Score | Priority | Status Summary |
| :--- | :--- | :--- | :--- |
| 1. NestJS Best Practices | X.X/10 | [High/Med/Low] | Brief status |
| 2. Node.js Best Practices | X.X/10 | [High/Med/Low] | Brief status |
| 3. TypeORM Best Practices | X.X/10 | [High/Med/Low] | Brief status |
| 4. Architecture | X.X/10 | [High/Med/Low] | Brief status |
| 5. Code Quality | X.X/10 | [High/Med/Low] | Brief status |
| 6. Performance | X.X/10 | [High/Med/Low] | Brief status |
| 7. Scalability | X.X/10 | [High/Med/Low] | Brief status |
| 8. Maintainability | X.X/10 | [High/Med/Low] | Brief status |
| 9. Readability | X.X/10 | [High/Med/Low] | Brief status |
| 10. Database Design | X.X/10 | [High/Med/Low] | Brief status |
| 11. Dependency Injection | X.X/10 | [High/Med/Low] | Brief status |
| 12. Module Design | X.X/10 | [High/Med/Low] | Brief status |
| 13. Error Handling | X.X/10 | [High/Med/Low] | Brief status |
| 14. Logging | X.X/10 | [High/Med/Low] | Brief status |
| 15. Validation | X.X/10 | [High/Med/Low] | Brief status |
| 16. Caching | X.X/10 | [High/Med/Low] | Brief status |
| 17. Testing Readiness | X.X/10 | [High/Med/Low] | Brief status |
| 18. Cloud Readiness | X.X/10 | [High/Med/Low] | Brief status |
| 19. Microservice Readiness | X.X/10 | [High/Med/Low] | Brief status |

## Detailed Domain Assessments (All 19 Domains)
[Sequential evaluation for each of the 19 domains formatted according to the Category Schema]

## Dependency & Package Optimization Analysis
Detailed analysis of third-party packages in `package.json`:
- **Packages Recommended for Replacement / Upgrade**
- **Bloatware / Heavy Dependencies to Prune**
- **Security & Maintenance Evaluation**

## Prioritized Modernization & Refactoring Roadmap
1. Phase 1: High-Priority Security & Stability Fixes (Week 1)
2. Phase 2: Architectural & Dependency Optimization (Weeks 2-3)
3. Phase 3: Observability, Caching & Cloud Readiness (Weeks 4-5)

## Summary of Total Estimated Engineering Effort
- **Total Story Points**: XX Points
- **Estimated Hours**: YY Hours
```

---

## Log Specification (`reports/YYYY-MM-DD/analysis-log.md`)

Maintain a consolidated log entry for Skill 4 inside `reports/YYYY-MM-DD/analysis-log.md`:

```markdown
## Execution Log - Skill 4 (Engineering Compliance & Scorecard)
- **Date**: YYYY-MM-DD
- **Git Commit Hash**: `[commit_hash]`
- **Branch**: `[branch_name]`
- **Start Time**: HH:MM:SS
- **End Time**: HH:MM:SS
- **Execution Duration**: XX mins
- **Target Report File**: `reports/YYYY-MM-DD/04-engineering-compliance.md`
- **Modules Analyzed**: Total Count
- **Files Analyzed**: Total Count
- **Global Compliance Score**: X.X / 10.0
- **Total Estimated Engineering Effort**: XX Points / YY Hours
- **Resume Point / Pending Tasks**: Done or next steps
```

---

## Constraints
1. **Exhaustive Evaluation**: You MUST evaluate all 19 domain categories without exception.
2. **Package Optimization Emphasis**: Explicitly audit third-party libraries and methods to ensure optimal performance and memory footprint.
3. **Directory Isolation**: Store ALL reports strictly in the per-day directory `reports/YYYY-MM-DD/`.
4. **No Code Mutation**: Do not alter application code directly. Only generate report markdown files and update the log.
5. **Actionable Estimates**: Every domain evaluation MUST include estimated engineering effort (Story Points / Hours) and ROI impact.
6. **Persistence Integrity**: Save and commit findings to disk immediately upon discovering each domain's score.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** This section is editorial context and is NOT part of the executable audit instructions. The executing model MUST ignore it when running the skill.

**Purpose.** Produces a defensible engineering-maturity scorecard: 19 scored domains plus package-optimization analysis, the artifact a CTO or architecture review board will actually read.

**Key design decisions.** (1) 19 domains chosen to span the full engineering surface — framework idioms, typing, DI, error handling, observability, testing, cloud readiness — with each requiring an explicit X.X/10 score and a priority; (2) evidence rule: every score must cite file:line references, which is what makes the scorecard auditable rather than vibes-based; (3) package-vs-package comparisons (Pino vs Winston, Zod vs class-validator) give concrete, actionable dependency guidance; (4) mandatory effort/ROI estimates turn the report into a prioritization instrument.

**Coverage & limitations.** The unavoidable weakness is subjectivity: nothing here forces a rubric, so two executions could score the same repo differently (file:line evidence mitigates but doesn't eliminate this). The fixed 19-domain template is also token-heavy for small-context models on large repos.

**Recommended enhancements.** Add per-domain scoring anchors (e.g., 0–4 rubric descriptors per domain) to stabilize scores across runs; expose the weights used for the overall 10.0 so readers can see how the aggregate was derived; and add a "score delta vs previous run" field so the suite's resume capability produces trend data.
