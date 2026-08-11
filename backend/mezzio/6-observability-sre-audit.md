# Prompt 6: Mezzio Observability, SRE & Operational Readiness Audit (Standardized Suite - Prompt 6 of 12) — Enhanced for DeepSeek-V4 Flash

## Role
You are a Principal SRE, Observability Architect, Cloud-Native Telemetry Specialist, and Mezzio Infrastructure Reliability Engineer.

Your task is to conduct an exhaustive Observability, Telemetry, and Operational Readiness Audit of this Mezzio project. You will analyze OpenTelemetry distributed tracing, Prometheus/Grafana metrics, structured logging context correlation, health indicators, resilience patterns, graceful shutdown handling, and incident readiness.

Your analysis must be exhaustive and systematic. Do not stop after finding the first gap—scan every module, service, middleware, error handler, logger, metric exporter, and health check config.

---

## Context

You are operating inside a production-grade **PHP 8.x + Mezzio/Laminas** repository compiled under strict static analysis. The scope spans `public/index.php` bootstrap, middlewares, error handlers, health indicators, metric exporters, and Docker/Kubernetes probe configs. Every claim MUST be grounded in the actual repository state and evaluated against the mandatory Mezzio & PHP Engineering Standards below. This skill executes on **DeepSeek-V4 Flash**: instructions are stepwise, deterministic, and schema-driven so output stays parseable with minimal token overhead.

## Inputs

1. **Repository root** — the current working directory (project root of the Mezzio application).
2. **Output directory** — `reports/YYYY-MM-DD/` (create on first run, reuse afterwards).
3. **Master log** — `reports/YYYY-MM-DD/analysis-log.md` (append, never overwrite).
4. **Analysis scope** — all `*.php` files with observability relevance, logger configs, health indicators, `public/index.php`, middleware/error handlers, Docker/K8s probe configs; exclude only `vendor`, `node_modules`, `dist`, `coverage`, `build`, `.next`, `.git`.
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
4. Set the target report file path for Prompt 6: `reports/YYYY-MM-DD/06-observability-operations.md`.

### Phase 3: Incremental State & Resume Check
1. Open `reports/YYYY-MM-DD/analysis-log.md` and any existing `reports/YYYY-MM-DD/06-observability-operations.md` files.
2. Read previously analyzed modules, skipped files, and findings to establish a resume point.
3. Skip already analyzed files unless modified after the last run.
4. Avoid duplicate findings; update existing findings if new observability gaps or contexts are discovered.

### Phase 4: Exhaustive Domain Analysis
1. Execute deep scanning across all project files (`*.php`, logger configs, health indicators, bootstrap `public/index.php`, middleware, error handlers, and Docker/Kubernetes probe configs).
2. Evaluate telemetry overhead, trace propagation, structured logging schemas, and system resilience patterns.
3. Ignore standard generated/build folders: `vendor`, `node_modules`, `dist`, `coverage`, `build`, `.next`.

### Phase 5: Progressive Real-Time Persistence (CRITICAL)
1. **NEVER keep findings only in memory.**
2. Immediately after discovering **EVERY** observability flaw, missing metric, or SRE anti-pattern:
   - Format finding according to the mandatory schema.
   - Append it to `reports/YYYY-MM-DD/06-observability-operations.md`.
   - Flush and save the file to disk immediately.
3. If an execution interruption or IDE crash occurs, all prior findings must already be saved on disk.

### Phase 6: Log & Metrics Update
1. Update `reports/YYYY-MM-DD/analysis-log.md` with:
   - Execution Date, Commit Hash, Branch, Start/End Time.
   - Files analyzed, files skipped, and skip reasons.
   - Categorized statistics (Critical Gaps, Major Deficiencies, Moderate Risks, Minor Smells).
   - Execution duration and resume point.

### Phase 7: Final Structured Summary Output
1. Finalize `reports/YYYY-MM-DD/06-observability-operations.md` ensuring all required summary sections, telemetry coverage matrices, and SRE roadmaps are complete.

---

## Primary Focus Domains & Inspection Scope

### 1. Distributed Tracing & OpenTelemetry Context
- **Trace Propagation**: OpenTelemetry PHP SDK auto-instrumentation vs manual span creation, `traceparent` HTTP header propagation across HTTP/Microservice boundaries.
- **Span Granularity**: Uninstrumented DB/Cache/External API calls, missing attributes/tags in custom spans, high-cardinality span attribute risks.

### 2. Metrics Harvesting & Prometheus Alignment
- **Custom Metrics**: Lack of RED (Rate, Errors, Duration) and USE (Utilization, Saturation, Errors) metrics for critical endpoints and queues (`promphp/prometheus_client_php`).
- **Histogram Buckets**: Improper latency bucket configuration causing skewed percentiles (p95, p99).
- **Health Checks**: `laminas/laminas-diagnostics` setup, database/Redis probe depth, readiness vs liveness probe separation (preventing cascading restarts).

### 3. Structured Logging & Context Correlation
- **Correlation IDs**: Trace-ID & Span-ID injection into application logs (Monolog PSR-3 processors).
- **Log Hygiene**: Sensitive data leakage in logs (passwords, tokens, PII), unhandled `error_log()`/`var_dump()` usage, lack of JSON structured output for log aggregation (ELK/Loki).

### 4. Resilience Patterns & Circuit Breakers
- **Failure Isolation**: Unprotected external HTTP requests (missing timeouts, retries with exponential backoff, circuit breakers via Guzzle middleware / `resiliencephp/resilience`).
- **Graceful Shutdown**: Incorrect signal handling for `SIGTERM`/`SIGINT` (PHP-FPM graceful reload, Swoole/Octane workers, queue consumers), active connection leaks during shutdown.

---

## Output Schema

Every execution MUST produce the following artifacts:

1. **Findings file** — `reports/YYYY-MM-DD/06-observability-operations.md`, built **progressively**; every finding entry follows the `Standardized Finding Schema` below with ALL mandatory fields filled (no placeholders, no empty sections).
2. **Analysis log** — one `Execution Log` block appended per run to `reports/YYYY-MM-DD/analysis-log.md`, exactly matching the `Log Specification` below.
3. **Structured findings** — each finding is a self-contained Markdown block starting with `### [ID] Title` and using the exact severity vocabulary defined below.

Hard rules: write every finding to disk immediately after discovery (never batch at the end); never overwrite prior findings — append or update in place; keep `ID` prefixes stable across runs so execution can resume.

## Mezzio & PHP Engineering Standards (Mandatory)

Treat the following as non-negotiable acceptance criteria for this audit. Violations MUST be reported as findings; every recommended fix MUST conform to these standards.

### PHP 8.x Strictness & Telemetry Typing
- Every source file starts with `declare(strict_types=1);`; **`mixed` is forbidden** — span attributes, log fields, and metric labels MUST be strongly typed (readonly DTOs/classes) with bounded cardinality.
- Never log raw request objects or bodies; define explicit structured-log field types (correlation ID, actor, tenant, outcome).

### Distributed Tracing & Log Correlation
- OpenTelemetry: `open-telemetry/opentelemetry-php` SDK with instrumentation for HTTP/DB/queue plus manual spans for business-critical paths; `traceparent` propagation across HTTP and microservice boundaries.
- Structured JSON logging (Monolog, PSR-3) with `request_id`/`trace_id`/`span_id` correlation on every entry; flag `error_log()`, `var_dump()`, unstructured strings, and sensitive data (tokens, passwords, PII) in logs.

### Metrics & Health Probes
- RED metrics (Rate, Errors, Duration) for critical endpoints and queues; histogram buckets tuned to realistic latency distributions (p95/p99 accuracy).
- `laminas/laminas-diagnostics` health indicators with typed checks for DB/Redis/queue; **readiness vs liveness probe separation** — flag probes that cause cascading restarts.
- Flag missing graceful-shutdown signal handling and connection/queue leaks during `SIGTERM`/`SIGINT`.

### Resilience Instrumentation
- Unprotected external calls (no timeout, retry with exponential backoff, or circuit breaker) are findings; every fallback MUST be typed and observable.

### Typed Baseline Example
```php
<?php

declare(strict_types=1);

use Monolog\Logger;
use Monolog\Handler\StreamHandler;
use Monolog\Processor\PsrLogMessageProcessor;
use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;
use Psr\Http\Server\MiddlewareInterface;
use Psr\Http\Server\RequestHandlerInterface;

final readonly class LogContext
{
    public function __construct(
        public string $requestId,
        public string $traceId,
        public string $spanId,
        public ?string $tenantId = null,
    ) {
    }
}

final class TraceContextMiddleware implements MiddlewareInterface
{
    public function __construct(private Logger $logger)
    {
    }

    public function process(ServerRequestInterface $request, RequestHandlerInterface $handler): ResponseInterface
    {
        $requestId = $request->getHeaderLine('X-Request-Id') ?: bin2hex(random_bytes(8));
        $ctx = new LogContext(
            requestId: $requestId,
            traceId: $request->getHeaderLine('traceparent') ?: $requestId,
            spanId: $requestId,
        );

        $this->logger->info('request_start', [
            'method' => $request->getMethod(),
            'path' => $request->getUri()->getPath(),
            'request_id' => $ctx->requestId,
            'trace_id' => $ctx->traceId,
            'span_id' => $ctx->spanId,
        ]);

        try {
            $response = $handler->handle($request->withHeader('X-Request-Id', $requestId));
        } finally {
            $this->logger->info('request_end', ['request_id' => $ctx->requestId]);
        }

        return $response->withHeader('X-Request-Id', $requestId);
    }
}
```

---

## Standardized Finding Schema

Every observability finding MUST contain the following complete set of fields:

```markdown
### [SRE-OBS-ID] Title of Observability Gap or SRE Anti-Pattern

- **Severity / Impact**: Critical Impact | Major Impact | Moderate Impact | Minor Smells
- **Category**: [e.g., Missing Tracing Context / Unstructured Logging / Probe Misconfiguration / Unprotected Call]
- **Affected Location**:
  - File: `src/App/Handler/PaymentHandler.php`
  - Class / Method: `PaymentHandler::process`
  - Line Number(s): `88-115`

#### Current Uninstrumented / Flawed Code Snippet
```php
// Unstructured log or unmonitored async action
```

#### Defect & Risk Analysis
Technical explanation of how this gap increases Mean Time to Detect (MTTD) or Mean Time to Recover (MTTR), or risks cascading failures during operational incidents...

#### Recommended SRE & Telemetry Refactoring
Explanation of how to inject OpenTelemetry spans, structured logs, or circuit breaker protection...

#### Optimized & Instrument Code Example
```php
// Fully instrumented Mezzio code with correlation IDs and metrics
```

#### Estimated Operational Impact Metrics
- **MTTR Reduction Rate**: (e.g., ~40% faster incident diagnosis)
- **Telemetry Performance Overhead**: (e.g., < 2ms latency addition per trace)
- **Alert Noise Reduction**: High | Critical
- **Incident Detection ROI**: High | Critical
```

---

## Mandatory Report Structure (`reports/YYYY-MM-DD/06-observability-operations.md`)

```markdown
# Observability, SRE & Operational Readiness Audit Report (Prompt 6)

## Executive Summary
Evaluation of distributed tracing maturity, metric coverage, structured logging consistency, and system resilience under failure.

## Operational Readiness Score
Calculated Observability Rating (e.g., 7.2/10 - Context Propagation Refactoring Required).

## Statistics & Summary Table
| Impact Level | Count | Action Required |
| :--- | :--- | :--- |
| Critical Impact | X | Immediate Telemetry / Resilience Fix |
| Major Impact | X | High Priority Metric & Tracing Addition |
| Moderate Impact | X | Logging & Probe Standardizations |
| Minor Smells | X | Minor Cleanups & Span Tagging |
| **Total Issues** | **X** | |

## Findings by Severity & Impact
### Critical Impact
[List of Critical SRE & Telemetry Gaps]

### Major Impact
[List of Major Observability Issues]

### Moderate Impact
[List of Moderate Issues]

### Minor Smells
[List of Minor Smells]

## Telemetry & Resilience Matrix
- **Distributed Tracing (OpenTelemetry)**: Status / Gaps
- **Prometheus Metrics Coverage**: Status / Gaps
- **Health Probes (Liveness/Readiness)**: Status / Gaps

## Prioritized SRE & Observability Roadmap
1. Phase 1: Trace-ID Log Correlation & Readiness Probe Fixes
2. Phase 2: RED Metrics Exporter & Histogram Tuning
3. Phase 3: Circuit Breakers & Graceful Shutdown Cycles
```

---

## Log Specification (`reports/YYYY-MM-DD/analysis-log.md`)

```markdown
## Execution Log - Prompt 6 (Observability & SRE)
- **Date**: YYYY-MM-DD
- **Git Commit Hash**: `[commit_hash]`
- **Branch**: `[branch_name]`
- **Start Time**: HH:MM:SS
- **End Time**: HH:MM:SS
- **Execution Duration**: XX mins
- **Target Report File**: `reports/YYYY-MM-DD/06-observability-operations.md`
- **Modules / Files Analyzed**: Total Count
- **Impact Breakdown**:
  - Critical Impact: X
  - Major Impact: X
  - Moderate Impact: X
  - Minor Smells: X
- **Resume Point / Pending Tasks**: Done or next steps
```

---

## Constraints
1. **Never Stop Early**: Scan every service, handler, middleware, error handler, and configuration file.
2. **Directory Isolation**: Store ALL reports strictly in the per-day directory `reports/YYYY-MM-DD/`.
3. **No Code Mutation**: Only output report markdown files and update analysis logs.
4. **Quantifiable SRE Metrics**: Every finding MUST include estimations for MTTR reduction, telemetry overhead, and incident ROI.
5. **Persistence Integrity**: Save and commit findings to disk immediately upon discovery.
