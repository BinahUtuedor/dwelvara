# ADR-007: Synchronous and asynchronous cross-runtime communication

- **Status:** Accepted
- **Date:** 2026-08-21

## Context

Some future intelligence results will be needed immediately while heavier work must survive background processing. The two languages need interoperable transport semantics, authentication, observability, and failure isolation without Kafka or framework-private queue protocols.

## Decision

Use authenticated HTTP for user-blocking/immediate Node.js-to-Python work. Centralise all calls in `apps/web/src/infrastructure/python-client/`. Protected FastAPI endpoints use a rotatable internal bearer service credential in Phase 0; `/health` may remain unauthenticated. Calls have bounded timeouts, explicit error mapping, correlation IDs, narrowly safe retries, and graceful degradation. Production workload identity is deferred beyond Phase 0.

Use Redis Streams with consumer groups for initial asynchronous communication. Node produces and Python consumes an explicit versioned, language-neutral JSON job envelope containing at least `schemaVersion`, `jobId`, `jobType`, `occurredAt`, `correlationId`, `attempt`, and `payload`. Do not rely on BullMQ, Celery, or any private cross-language queue representation.

Processing provides idempotency by `jobId`, acknowledgement after committed results, bounded retries, stale pending-entry recovery, dead-letter handling, status tracking, and structured observability. Python owns derived job results in `intelligence`. Node observes those results through a protected Python endpoint rather than reading the intelligence schema.

Phase 0 proves only `foundation.ping` through HTTP and one Redis job/result flow. Web liveness and foundation operation must continue when Python is unavailable. No cloud-specific infrastructure, Kafka, or product intelligence job is introduced.

## Alternatives considered

- HTTP for all work: rejected because long-running work needs durable background handling.
- Redis lists: simple, but Streams provide consumer groups, acknowledgements, and pending recovery with fewer custom reliability mechanisms.
- BullMQ-to-Celery interoperability: rejected because private protocols are not a shared contract.
- Kafka: rejected as unnecessary MVP infrastructure.
- Node reading Python result tables directly: rejected because it violates schema ownership and couples persistence.

## Consequences

- The platform supports immediate and background paths without introducing another service.
- Queue envelope and result schemas are shared contracts and require compatibility tests.
- Workers must implement retry, reclaim, DLQ, idempotency, and observability explicitly.
- Python outages degrade/delay intelligence but do not block the Node.js foundation.
