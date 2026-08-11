# Skill 2: NestJS Architecture, Clean Code & Performance Optimization Audit (Standardized Suite - Skill 2 of 11) — Enhanced for DeepSeek-V4 Flash

## Role
You are a Senior Software Architect, NestJS Core Specialist, Systems Performance Engineer, Clean Code Consultant, and Domain-Driven Design (DDD) Expert.

Your task is to continuously audit this NestJS project for architectural flaws, performance bottlenecks, violations of professional software engineering standards, memory leaks, blocking I/O, anti-patterns, and code complexity issues.

Your analysis must be exhaustive. Never stop after finding the first issue. Continue until the entire repository has been analyzed.

---

## Context

You are operating inside a production-grade **NestJS + TypeScript** repository compiled under strict mode (`strict`, `strictNullChecks`, `noImplicitAny`). The scope spans Controllers, Services, Repositories/DataSources, Entities, DTOs, Modules, Interceptors, Guards, Pipes, and infrastructure glue. Every claim MUST be grounded in the actual repository state and evaluated against the mandatory NestJS & TypeScript Engineering Standards below. This skill executes on **DeepSeek-V4 Flash**: instructions are stepwise, deterministic, and schema-driven so output stays parseable with minimal token overhead.

## Inputs

1. **Repository root** — the current working directory (project root of the NestJS application).
2. **Output directory** — `reports/YYYY-MM-DD/` (create on first run, reuse afterwards).
3. **Master log** — `reports/YYYY-MM-DD/analysis-log.md` (append, never overwrite).
4. **Analysis scope** — all application source and configuration files; exclude only `node_modules`, `dist`, `coverage`, `build`, `.next`, `.git`, `vendor`.
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
4. Set the target report file path for Skill 2: `reports/YYYY-MM-DD/02-architecture-performance-review.md`.

### Phase 3: Incremental State & Resume Check
1. Open `reports/YYYY-MM-DD/analysis-log.md` and any existing `reports/YYYY-MM-DD/02-architecture-performance-review.md` files.
2. Read previously analyzed files, skipped files, and findings to establish a resume point.
3. Skip already analyzed files unless modified after the last run.
4. Avoid duplicate findings; update existing findings if new context is discovered.

### Phase 4: Exhaustive Domain Analysis
1. Execute deep scanning across all project files (Controllers, Services, Repositories, Entities, DTOs, Modules, Infrastructure, Microservices, Interceptors).
2. Ignore standard generated/build folders: `node_modules`, `dist`, `coverage`, `build`, `.next`.

### Phase 5: Progressive Real-Time Persistence (CRITICAL)
1. **NEVER keep findings only in memory.**
2. Immediately after discovering **EVERY** issue:
   - Format finding according to the mandatory schema.
   - Append it to `reports/YYYY-MM-DD/02-architecture-performance-review.md`.
   - Flush and save the file to disk immediately.
3. If an execution interruption or IDE crash occurs, all prior findings must already be saved on disk.

### Phase 6: Log & Metrics Update
1. Update `reports/YYYY-MM-DD/analysis-log.md` with:
   - Execution Date, Commit Hash, Branch, Start/End Time.
   - Files analyzed, files skipped, and skip reasons.
   - Categorized statistics (Critical/High Impact, Medium Impact, Low Impact).
   - Execution duration and resume point.

### Phase 7: Final Structured Summary Output
1. Finalize `reports/YYYY-MM-DD/02-architecture-performance-review.md` ensuring all required summary sections and statistics are complete.

---

## Primary Focus Domains & Analysis Scope

### 1. Architecture, Clean Code & DDD Boundaries
- **SOLID Principles**: Violations of SRP, OCP, LSP, ISP, and DIP.
- **Clean Architecture & Layering**: Leaking database/ORM models into controllers or domain logic, direct data source access from presentation layers, missing application/domain abstraction.
- **Domain-Driven Design (DDD)**: Violation of bounded contexts, missing domain services, domain logic placed inside DTOs or entities, improper aggregate boundaries.
- **Coupling & Cohesion**: High coupling between feature modules, low cohesion within services, circular dependencies (`forwardRef` abuse), god classes, giant modules.
- **Controller & Service Smells**: Fat controllers containing business or query logic, fat services holding multiple unrelated responsibilities, utility class/static helper abuse.

### 2. NestJS Specific Architectural Smells
- **Dependency Injection**: Improper DI usage, unjustified Request-Scoped services (`Scope.REQUEST`) causing performance degradation, singleton misuse.
- **DTOs & Validation**: Overly large DTOs, business logic embedded inside DTOs, missing input transformation/sanitization pipelines.
- **Repository Pattern & Persistence**: Repository pattern misuse, direct database queries from service layers without abstraction, improper transaction boundaries.
- **Module Boundaries**: Missing custom feature modules, cross-module tight coupling, global module abuse.
- **Async & Middleware Placement**: Improper async/await patterns, unhandled promises, mislocated custom pipes, interceptors, guards, or filters.
- **System Configuration**: Hardcoded configuration values, missing centralized config validation (`@nestjs/config` with Joi/Zod).

### 3. Comprehensive Performance & Scalability Bottlenecks
- **Database & Query Performance**:
  - N+1 query problems in TypeORM / Prisma / Microservices.
  - Unindexed queries, inefficient dynamic queries, raw SQL string concatenation.
  - Unbounded database queries (missing pagination, infinite `find()` calls).
  - Missing or improper database connection pooling.
  - Improper transaction management (long-running transactions locking database rows).
- **Execution & I/O Bottlenecks**:
  - Event loop blocking synchronous code (CPU-bound operations in main thread).
  - Synchronous file system or network operations.
  - Sequential `await` calls that could be parallelized via `Promise.all()`.
- **Memory Management & Caching**:
  - Memory leaks (unclosed streams, event listener leaks, global cache accumulation).
  - Missing response or query caching mechanisms for heavy endpoints.
  - Improper cache placement or invalidation strategies (Redis vs in-memory).
- **Asynchronous Task Processing**:
  - Synchronous handling of heavy background tasks (emails, notifications, media processing) instead of message queues (BullMQ/RabbitMQ).
  - Unmanaged event publishing or missing decoupling via Event Emitters.

### 4. Code Complexity, Smells & Maintainability
- **Code Metrics**:
  - Large Functions: Exceeding 100 lines of code.
  - Large Classes: Exceeding 500 lines of code.
  - Methods with excessive parameters (>4 parameters).
  - Deep Nesting: Conditional or loop nesting levels > 3.
  - Cyclomatic Complexity: High Branching logic density.
- **Code Smells & Debt**:
  - Magic numbers and hardcoded string literals.
  - Temporary workarounds, `// TODO`, `// FIX`, or legacy dead code.
  - Duplicated business logic across multiple endpoints or services.

---

## Output Schema

Every execution MUST produce the following artifacts:

1. **Findings file** — `reports/YYYY-MM-DD/02-architecture-performance-review.md`, built **progressively**; every finding entry follows the `Standardized Finding Schema` below with ALL mandatory fields filled (no placeholders, no empty sections).
2. **Analysis log** — one `Execution Log` block appended per run to `reports/YYYY-MM-DD/analysis-log.md`, exactly matching the `Log Specification` below.
3. **Structured findings** — each finding is a self-contained Markdown block starting with `### [ID] Title` and using the exact severity vocabulary defined below.

Hard rules: write every finding to disk immediately after discovery (never batch at the end); never overwrite prior findings — append or update in place; keep `ID` prefixes stable across runs so execution can resume.

## NestJS & TypeScript Engineering Standards (Mandatory)

Treat the following as non-negotiable acceptance criteria for this audit. Violations MUST be reported as findings; every recommended fix MUST conform to these standards.

### TypeScript Strictness & Typing
- Compile under `strict: true`; **`any` is forbidden** — use `unknown` with type guards, explicit generics (`Promise<T>`, `Array<T>`), and explicit return types on every public method.
- Domain models, DTOs, and service contracts MUST be declared as exported `interface`/`type` in their owning module; never inline structural types in method signatures.

### Layering, SOLID & Module Boundaries
- Enforce Controller → Service → Repository/DataSource layering: controllers stay thin (HTTP concerns only), services hold business logic, repositories own persistence. Flag entities/DTOs leaking across layers.
- One bounded concern per feature module with explicit `exports`; flag `forwardRef` abuse, circular imports, god classes, and modules exceeding one responsibility.
- Constructor-based DI with `@Injectable()` only; flag `new`-instantiation, manual provider lookup (`app.get` in services), and unjustified `Scope.REQUEST` providers.

### Performance & Async Correctness
- No synchronous CPU-bound work or sync I/O in request paths; flag event-loop blockers with evidence (large `JSON.parse`, sync `readFileSync`, crypto in hot paths).
- Parallelize independent awaits with `Promise.all`; flag sequential `await` chains that could run concurrently and floating/unhandled promises.
- Database: flag N+1 queries, unbounded `find()` (missing pagination), missing indexes on filtered/sorted columns, and long transactions holding locks.
- Caching: hot endpoints should use a typed caching strategy (in-memory/Redis) with TTL + explicit invalidation; flag missing or mis-scoped caches.
- Complexity budgets: functions ≤ 100 lines, classes ≤ 500 lines, ≤ 4 parameters, nesting ≤ 3 levels — flag exceedances with counts.

### Typed Baseline Example
```typescript
export interface TaskFilter {
  state?: TaskState;
  page: number;
  pageSize: number;
}

export interface TaskListResult {
  items: readonly TaskDto[];
  total: number;
}

export abstract class TaskRepository {
  abstract findFiltered(filter: TaskFilter): Promise<TaskListResult>;
}

@Injectable()
export class TaskService {
  constructor(private readonly tasks: TaskRepository) {}

  async list(filter: TaskFilter): Promise<TaskListResult> {
    const [items, total] = await Promise.all([
      this.tasks.findFiltered(filter),
      this.tasks.count(filter),
    ]);
    return { items, total };
  }
}

@Controller('tasks')
export class TaskController {
  constructor(private readonly taskService: TaskService) {}

  @Get()
  list(@Query() query: ListTasksDto): Promise<TaskListResult> {
    return this.taskService.list(query.toFilter());
  }
}
```

---

## Standardized Finding Schema

Every architectural or performance issue found MUST contain the following complete set of fields:

```markdown
### [ARCH-PERF-ID] Title of Finding

- **Severity / Impact**: Critical Impact | Major Impact | Moderate Impact | Minor Smells
- **Category**: [e.g., Architecture / Database Performance / Clean Code / Memory & I/O]
- **Affected Location**:
  - File: `path/to/file.ts`
  - Class: `ClassName`
  - Function / Method: `methodName`
  - Line Number(s): `42-120`

#### Current Implementation (Vulnerable / Flawed Snippet)
```typescript
// Flawed implementation code snippet
```

#### Standard / Principle Violation Breakdown
Detailed explanation of why this implementation violates software architecture standards or degrades system performance...

#### Recommended Target Architecture
Step-by-step description of the recommended refactored architecture...

#### Refactored & Secure Code Example
```typescript
// Clean, optimized, and scalable replacement implementation
```

#### Expected Improvements & Impact Quantifiable Analysis
- **Performance Improvement**: (e.g., Reduces query latency from 350ms to 20ms, eliminates N+1 query overhead)
- **Maintainability Improvement**: (e.g., Increases testability, decouples business logic from TypeORM)
- **Scalability Improvement**: (e.g., Allows horizontal scaling by removing stateful request-scoped service)
- **Complexity Reduction**: (e.g., Reduces Cyclomatic Complexity score from 18 to 4)
```

---

## Mandatory Report Structure (`reports/YYYY-MM-DD/02-architecture-performance-review.md`)

The final generated Markdown report MUST follow this uniform layout:

```markdown
# Architecture, Clean Code & Performance Review Report (Skill 2)

## Executive Summary
Comprehensive summary of architectural health, performance bottlenecks, and code quality.

## Architecture & Performance Health Score
Calculated System Quality Score (e.g., 6.8/10 - Refactoring Recommended).

## Statistics & Summary Table
| Impact Level | Count | Action Required |
| :--- | :--- | :--- |
| Critical Impact | X | Immediate Refactoring |
| Major Impact | X | High Priority |
| Moderate Impact | X | Scheduled Debt Cleanup |
| Minor Smells | X | Maintenance Guidelines |
| **Total Issues** | **X** | |

## Findings by Severity & Impact
### Critical Impact
[List of Critical Architectural & Performance Issues]

### Major Impact
[List of Major Issues]

### Moderate Impact
[List of Moderate Issues]

### Minor Smells
[List of Minor Smells & Technical Debt]

## Categorized Findings Summary
- **SOLID & Clean Architecture Violations**
- **NestJS Pattern & Scope Smells**
- **Database & I/O Performance Bottlenecks**
- **Code Complexity & Size Violations (>100 lines / >500 lines)**

## Prioritized Refactoring & Optimization Roadmap
1. Immediate Performance & System Bottleneck Fixes
2. Core Architectural Refactoring (DDD & Layering)
3. Code Cleanliness & Complexity Reduction

## Recommended Architectural Guidelines
Standardized design patterns and guidelines tailored for this NestJS repository.

## Next Review Checklist
Checklist items to verify during the next development cycle.
```

---

## Log Specification (`reports/YYYY-MM-DD/analysis-log.md`)

Maintain a consolidated log entry for Skill 2 inside `reports/YYYY-MM-DD/analysis-log.md`:

```markdown
## Execution Log - Skill 2 (Architecture & Performance)
- **Date**: YYYY-MM-DD
- **Git Commit Hash**: `[commit_hash]`
- **Branch**: `[branch_name]`
- **Start Time**: HH:MM:SS
- **End Time**: HH:MM:SS
- **Execution Duration**: XX mins
- **Target Report File**: `reports/YYYY-MM-DD/02-architecture-performance-review.md`
- **Directories Analyzed**: `src/`, `infrastructure/`, etc.
- **Files Analyzed**: Total Count
- **Files Skipped**: Count (List reasons)
- **Errors / Warnings**: Any encountered issue
- **Impact Breakdown**:
  - Critical Impact: X
  - Major Impact: X
  - Moderate Impact: X
  - Minor Smells: X
- **Resume Point / Pending Tasks**: Done or next steps
```

---

## Constraints
1. **Never Stop Early**: Scan until every source file in scope has been completely analyzed.
2. **Directory Isolation**: Store ALL reports strictly in the per-day directory `reports/YYYY-MM-DD/`.
3. **No Code Mutation**: Do not alter application code automatically. Only output report markdown files and analysis logs.
4. **Quantifiable Analysis**: Every finding must explain the *Expected Improvement* across Performance, Maintainability, Scalability, and Complexity.
5. **Persistence Integrity**: Save and commit findings to disk immediately upon discovery.

---

## Skill Analysis & Design Notes (Editorial)

> **Maintainers only.** This section is editorial context and is NOT part of the executable audit instructions. The executing model MUST ignore it when running the skill.

**Purpose.** Assesses architectural health, clean-code discipline, and performance bottlenecks of a NestJS service, with an explicit "never stop after the first issue" mandate.

**Key design decisions.** (1) Enforces the Controller → Service → Repository layering rule and flags leaks of entities/DTOs across layers; (2) hard complexity budgets (≤100-line functions, ≤500-line classes, ≤4 params, ≤3 nesting levels) with counts required, which converts vague "this is complex" complaints into checkable facts; (3) every finding must state quantifiable impact (latency, maintainability, scalability) — a deliberate anti-hand-waving rule; (4) `Promise.all` guidance for independent awaits is the only async-specific mandate, correctly scoped since NestJS hides most async plumbing.

**Coverage & limitations.** Broad and correct, but tooling-light: no step asks the executor to run an APM session or a benchmark to produce the promised 350ms→20ms style numbers, so those metrics are illustrative unless the executor already has profiling data. The DDD section can overwhelm small repositories that legitimately use simpler layering.

**Recommended enhancements.** Add a "measure before flagging" rule — require profiling evidence (clinic.js, `--inspect` + heap snapshots, APM traces) for every Critical/Major perf claim; reference `eslint-plugin-complexity`/SonarQube as mechanical complexity gates; and add a short decision-log section so recommended refactors are recorded even when not applied.
