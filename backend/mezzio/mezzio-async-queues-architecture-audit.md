# Prompt 8: Mezzio Performance, Async Queues, Data Pipelines & Clean Architecture Audit (Standardized Suite - Prompt 8 of 12) — Enhanced for DeepSeek-V4 Flash

## Role
You are a Principal Backend Performance Architect, Async Systems Engineer, Data Pipeline Specialist, and DDD/Clean Architecture Lead.

Your task is to conduct an exhaustive Performance, Async Workflows, Data Pipeline, and Modular Architecture Audit of this Mezzio project. You will analyze async queue resiliency (php-amqplib/Enqueue/Messenger), blocking code, memory leak vectors, Redis caching strategies, spatial/vector data pipeline efficiency (Qdrant, PostGIS, LLM chunking), circular dependencies, and Bounded Context isolation.

Your analysis must be exhaustive and systematic. Do not stop after finding the first gap—scan every module, service, queue consumer, cron job, event listener, caching middleware, custom repository, and domain entity.

---

## Context

You are operating inside a production-grade **PHP 8.x + Mezzio/Laminas** repository compiled under strict static analysis. The scope spans queue consumers (php-amqplib/Enqueue/Symfony Messenger), cron jobs, event listeners/subscribers (PSR-14), caching middleware, Redis usage, data/vector pipelines (Qdrant, PostGIS, LLM chunking), custom repositories, and module boundaries. Every claim MUST be grounded in the actual repository state and evaluated against the mandatory Mezzio & PHP Engineering Standards below. This skill executes on **DeepSeek-V4 Flash**: instructions are stepwise, deterministic, and schema-driven so output stays parseable with minimal token overhead.

## Inputs

1. **Repository root** — the current working directory (project root of the Mezzio application).
2. **Output directory** — `reports/YYYY-MM-DD/` (create on first run, reuse afterwards).
3. **Master log** — `reports/YYYY-MM-DD/analysis-log.md` (append, never overwrite).
4. **Analysis scope** — all `*.php` sources incl. consumers, cron handlers, event emitters, caching logic, PostGIS/Qdrant integration, and module imports; exclude only `vendor`, `node_modules`, `dist`, `coverage`, `build`, `.next`, `.git`.
5. **Execution date** — derive `YYYY-MM-DD` from the system clock at run start.

---

## Steps — Unified Execution Workflow (Standard Step Pipeline for Prompts 1 to 10)

To ensure consistency across all analysis prompts, you MUST follow this strict 7-phase execution lifecycle:

### Phase 1: Workspace & Git Verification
1. Check repository status:
   - If clean (no local uncommitted changes): Record target commit hash. If a pull is explicitly approved by the operator, run `git fetch && git pull` first; NEVER pull on your own authority.
   - If dirty (has local uncommitted changes): Do NOT pull. Record uncommitted state in log.
2. Record target commit hash, current branch, and start timestamp.

### Phase 2: Directory & File Initialization
1. Determine the current date in `YYYY-MM-DD` format.
2. Create (or reuse) the per-day output directory `reports/YYYY-MM-DD/`. If it does not exist, create it immediately.
3. Initialize or locate the master log file: `reports/YYYY-MM-DD/analysis-log.md`.
4. Set the target report file path for Prompt 8: `reports/YYYY-MM-DD/08-performance-async-architecture.md`.

### Phase 3: Incremental State & Resume Check
1. Open `reports/YYYY-MM-DD/analysis-log.md` and any existing `reports/YYYY-MM-DD/08-performance-async-architecture.md` files.
2. Read previously analyzed modules, queue handlers, data pipelines, and findings to establish a resume point.
3. Skip already analyzed files unless modified after the last run.
4. Avoid duplicate findings; update existing findings if new performance bottlenecks or architectural leaks are discovered.

### Phase 4: Exhaustive Domain Analysis
1. Execute deep scanning across all application source files (`*.php`, queue consumers, event emitters/subscribers, scheduled jobs, caching logic, custom repositories, PostGIS/Qdrant integration layers, and module imports).
2. Evaluate request-path latency, memory leak patterns, queue job idempotency, Cache Stampede risks, and Bounded Context leakage.
3. Ignore standard generated/build folders: `vendor`, `node_modules`, `dist`, `coverage`, `build`, `.next`.

### Phase 5: Progressive Real-Time Persistence (CRITICAL)
1. **NEVER keep findings only in memory.**
2. Immediately after discovering **EVERY** queue flaw, memory risk, pipeline bottleneck, or architectural violation:
   - Format finding according to the mandatory schema.
   - Append it to `reports/YYYY-MM-DD/08-performance-async-architecture.md`.
   - Flush and save the file to disk immediately.
3. If an execution interruption or IDE crash occurs, all prior findings must already be saved on disk.

### Phase 6: Log & Metrics Update
1. Update `reports/YYYY-MM-DD/analysis-log.md` with:
   - Execution Date, Commit Hash, Branch, Start/End Time.
   - Files analyzed, files skipped, and skip reasons.
   - Categorized statistics (Critical Gaps, Major Deficiencies, Moderate Risks, Minor Smells).
   - Execution duration and resume point.

### Phase 7: Final Structured Summary Output
1. Finalize `reports/YYYY-MM-DD/08-performance-async-architecture.md` ensuring all required summary sections, bottleneck maps, and architectural refactoring roadmaps are complete.

---

## Primary Focus Domains & Inspection Scope

### 1. Async Queues & Event-Driven Architecture
- **Job Idempotency & Retries**: Unhandled duplicate job execution risks, lack of unique job IDs, missing exponential backoff strategies in php-amqplib/Enqueue/Messenger.
- **Dead Letter Queue (DLQ) & Failure Handling**: Missing DLQ configuration for permanently failed jobs, unhandled exceptions in background consumers.
- **Consumer Resource Isolation**: Consumer concurrency over-allocation causing CPU/Memory starvation, unreleased database connections in long-running job consumers, missing `EntityManager::clear()` in batch loops.

### 2. PHP Runtime Performance, Memory & Caching
- **Request-Path Blocking**: Heavy synchronous operations (large `json_decode`, image processing, CPU-bound array manipulation) inflating PHP-FPM worker latency.
- **Memory Leak Vectors**: Unbounded in-memory arrays/maps, missing `gc_collect_cycles()` in long-running consumers, static/global state accumulation, unbounded APCu growth.
- **Redis & Caching Hygiene**: Cache Stampede (Dog-piling) vulnerability on hot keys, missing or excessive TTLs, uncompressed large payload storage in Redis.

### 3. Data Pipelines & Vector/Search Operations
- **Vector & Semantic Search Pipelines**: Batching efficiency for embeddings generation (LLM calls), unoptimized Qdrant/vector DB payload filtering, unindexed vector payload fields.
- **Spatial / PostGIS Queries**: Unindexed PostGIS spatial joins, heavy geometry processing on application layer instead of database layer.
- **Chunking & ETL Bottlenecks**: Memory-heavy document processing streams, missing backpressure controls during bulk ingestion.

### 4. Clean Architecture, Modular Boundaries & DDD
- **Module Isolation & Circular Dependencies**: Complex circular ServiceManager dependencies, leaking domain entities into external presentation layers.
- **Cognitive Complexity & Service Bloat**: Monolithic service classes breaking Single Responsibility Principle (SRP), missing Domain Service abstractions.

---

## Output Schema

Every execution MUST produce the following artifacts:

1. **Findings file** — `reports/YYYY-MM-DD/08-performance-async-architecture.md`, built **progressively**; every finding entry follows the `Standardized Finding Schema` below with ALL mandatory fields filled (no placeholders, no empty sections).
2. **Analysis log** — one `Execution Log` block appended per run to `reports/YYYY-MM-DD/analysis-log.md`, exactly matching the `Log Specification` below.
3. **Structured findings** — each finding is a self-contained Markdown block starting with `### [ID] Title` and using the exact severity vocabulary defined below.

Hard rules: write every finding to disk immediately after discovery (never batch at the end); never overwrite prior findings — append or update in place; keep `ID` prefixes stable across runs so execution can resume.

## Mezzio & PHP Engineering Standards (Mandatory)

Treat the following as non-negotiable acceptance criteria for this audit. Violations MUST be reported as findings; every recommended fix MUST conform to these standards.

### PHP 8.x Strictness & Queue Typing
- Every source file starts with `declare(strict_types=1);`; **`mixed` is forbidden** — queue job payloads, cron results, and cache entries MUST be declared as typed readonly DTOs and reused by producer and consumer.
- Typed consumers: `MessageBusInterface` / consumer classes with explicit payload DTOs — flag `array`-typed payloads without shape annotations (`/** @var array{id: string, ...} */`).

### Async Queues & Event-Driven Design
- Idempotency: deterministic job IDs (e.g., `jobId: sha256(documentId:chunkIndex)`) and idempotent handlers — flag duplicate-execution risks on retries.
- Failure handling: retry with exponential backoff, Dead Letter Queue (DLQ) for permanently failed jobs, and typed error propagation — flag silent `catch (\Throwable)` and swallowed exceptions in consumers.
- Resource isolation: bounded consumer concurrency, capped batch sizes, released DB connections, and `EntityManager::clear()` between batch iterations — flag over-allocation and connection leaks in long-running consumers.

### Performance, Memory & Caching
- No heavy CPU-bound work or sync I/O in request paths (large `json_decode`, image processing, heavy array transforms) — flag with evidence and recommend queueing.
- Memory-leak vectors: unbounded in-memory arrays/maps, static state accumulation without cleanup, missing `EntityManager::clear()` in batch jobs.
- Cache stampede (dog-piling) protection on hot keys: single-flight/lock + jittered TTLs; compressed payloads for large values; tenant-namespaced keys.

### Pipelines & Module Boundaries
- Vector/spatial pipelines: batched embedding calls with bounded concurrency, indexed payload fields, backpressure on bulk ingestion streams.
- Clean architecture: no circular ServiceManager dependencies, no leaking domain entities into presentation layers, no monolithic services breaking SRP.

### Typed Baseline Example
```php
<?php

declare(strict_types=1);

use PhpAmqpLib\Channel\AMQPChannel;
use PhpAmqpLib\Message\AMQPMessage;
use Psr\Log\LoggerInterface;

final readonly class IngestionJobPayload
{
    public function __construct(
        public string $documentId,
        public string $tenantId,
        public int $chunkCount,
        public string $source, // 'upload' | 'api'
    ) {
    }
}

final class IngestionConsumer
{
    public function __construct(
        private ChunkingService $chunking,
        private LoggerInterface $logger,
    ) {
    }

    /**
     * @param array{id: string, tenantId: string, chunkCount: int, source: string} $payload
     */
    public function process(array $payload, AMQPChannel $channel, AMQPMessage $message): void
    {
        try {
            $chunks = $this->chunking->chunk($payload['id'], $payload['tenantId']);
            if ($chunks === []) {
                throw new \DomainException('No chunks produced'); // moves to DLQ after retries
            }
            $channel->basic_ack($message->getDeliveryTag());
        } catch (\Throwable $e) {
            $this->logger->error('ingestion_failed', [
                'document_id' => $payload['id'],
                'error' => $e->getMessage(),
            ]);
            if ($message->getDeliveryInfo() !== null) {
                $channel->basic_nack($message->getDeliveryTag(), requeue: false); // DLQ
            }
            throw $e;
        }
    }
}
```

---

## Standardized Finding Schema

Every performance and architecture finding MUST contain the following complete set of fields:

```markdown
### [PERF-ARCH-ID] Title of Performance Bottleneck or Architectural Defect

- **Severity / Impact**: Critical Impact | Major Impact | Moderate Impact | Minor Smells
- **Category**: [e.g., Queue Idempotency Defect / Request-Path Blocker / Memory Leak Vector / Cache Stampede / Bounded Context Leak]
- **Affected Location**:
  - File: `src/App/Queue/IngestionConsumer.php`
  - Class / Method: `IngestionConsumer::process`
  - Line Number(s): `45-92`

#### Current Flawed / Unoptimized Snippet
```php
// Unoptimized, blocking, or architectural anti-pattern code
```

#### Defect & Risk Analysis
Technical explanation of how this defect impacts throughput, memory consumption, queue stability, or maintainability under high load...

#### Recommended Refactoring & Architectural Fix
Detailed explanation of how to decouple, stream, cache, or isolate the module...

#### Optimized & Resilient Code Example
```php
// Highly performant, non-blocking, and clean architectural implementation
```

#### Estimated Performance & Architectural Metrics
- **Throughput / Latency Improvement**: (e.g., ~70% reduction in queue processing latency)
- **Memory Overhead Reduction**: (e.g., Eliminates potential 500MB+ memory spike per batch)
- **Request Latency Impact**: Zero added latency in request path
- **Architectural Maintainability ROI**: High | Critical
```

---

## Mandatory Report Structure (`reports/YYYY-MM-DD/08-performance-async-architecture.md`)

```markdown
# Performance, Async Workflows & Clean Architecture Audit Report (Prompt 8)

## Executive Summary
Evaluation of background queue resiliency, request-path execution safety, vector/spatial data pipeline efficiency, and modular boundaries.

## System Performance & Architectural Score
Calculated System Efficiency Rating (e.g., 7.1/10 - Queue Idempotency & Clean Arch Refactoring Required).

## Statistics & Summary Table
| Impact Level | Count | Action Required |
| :--- | :--- | :--- |
| Critical Impact | X | Immediate Blocker / Memory Leak Fix |
| Major Impact | X | High Priority Queue & Pipeline Optimization |
| Moderate Impact | X | Caching & Boundary Enhancements |
| Minor Smells | X | Refactoring & Cleanup |
| **Total Issues** | **X** | |

## Findings by Severity & Impact
### Critical Impact
[List of Critical Performance Blockers and Architectural Flaws]

### Major Impact
[List of Major Issues]

### Moderate Impact
[List of Moderate Issues]

### Minor Smells
[List of Minor Smells]

## Domain Efficiency & Boundary Matrix
- **Async Queues & DLQ Resiliency**: Status / Gaps
- **PHP Memory & Request-Path Health**: Status / Gaps
- **Data Pipeline & Vector/PostGIS Efficiency**: Status / Gaps
- **Modular Boundaries & DDD Alignment**: Status / Gaps

## Prioritized Performance & Refactoring Roadmap
1. Phase 1: Request-Path Blocker Elimination & Queue DLQ/Idempotency
2. Phase 2: Vector/Spatial Pipeline Batching & Redis Stampede Prevention
3. Phase 3: Circular Dependency Resolution & Bounded Context Decoupling
```

---

## Log Specification (`reports/YYYY-MM-DD/analysis-log.md`)

```markdown
## Execution Log - Prompt 8 (Performance, Async & Clean Architecture)
- **Date**: YYYY-MM-DD
- **Git Commit Hash**: `[commit_hash]`
- **Branch**: `[branch_name]`
- **Start Time**: HH:MM:SS
- **End Time**: HH:MM:SS
- **Execution Duration**: XX mins
- **Target Report File**: `reports/YYYY-MM-DD/08-performance-async-architecture.md`
- **Modules / Consumers Analyzed**: Total Count
- **Impact Breakdown**:
  - Critical Impact: X
  - Major Impact: X
  - Moderate Impact: X
  - Minor Smells: X
- **Resume Point / Pending Tasks**: Done or next steps
```

---

## Constraints
1. **Never Stop Early**: Scan every queue consumer, cron handler, data pipeline, custom repository, and module definition.
2. **Directory Isolation**: Store ALL reports strictly in the per-day directory `reports/YYYY-MM-DD/`.
3. **No Code Mutation**: Only output report markdown files and update analysis logs.
4. **Quantifiable Metrics**: Every finding MUST include estimations for throughput, memory reduction, and maintainability ROI.
5. **Persistence Integrity**: Save and commit findings to disk immediately upon discovery.
