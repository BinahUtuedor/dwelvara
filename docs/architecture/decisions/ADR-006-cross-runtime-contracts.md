# ADR-006: Canonical cross-runtime contracts

- **Status:** Accepted
- **Date:** 2026-08-21

## Context

TypeScript and Python need to exchange HTTP and job payloads. Independently authored validators would drift and a TypeScript interface alone cannot govern Python runtime validation.

## Decision

`packages/contracts` is the language-neutral source of truth. Define canonical JSON Schema/OpenAPI contracts with explicit schema versions, canonical valid/invalid examples, and compatibility rules. Every cross-runtime HTTP and job payload carries `schemaVersion`.

Mechanically generate or derive TypeScript/Zod and Python/Pydantic runtime validators/models from the canonical source wherever practical. Before selecting generation tooling, perform the approved small fidelity proof against the Phase 0 schemas, verify constraint and round-trip behaviour in both runtimes, then pin and record compatible stable tool versions. Do not independently author equivalent schemas. Mechanical adapters are allowed only when documented and tested against the canonical schema.

Additive optional changes may remain in a major version and consumers tolerate unknown optional fields. Breaking changes require a new major version and retirement plan. Canonical examples must pass equivalent JSON Schema, Zod, and Pydantic tests; generated-output freshness is checked in CI.

Phase 0 defines only the infrastructure `foundation.ping`, generic job envelope, and job-result proof contracts. It creates no marketplace domain contracts.

## Alternatives considered

- Hand-maintain Zod and Pydantic models: rejected because they can drift.
- TypeScript types as the source: rejected because they are not language-neutral runtime schemas.
- Pydantic/OpenAPI as the sole source: viable for Python-led APIs but insufficiently neutral for all HTTP and Redis payload ownership.
- Select a generator without a proof: rejected because generator fidelity varies by schema feature.

## Consequences

- Contract evolution is explicit and testable across languages.
- Generation becomes a build/CI concern with pinned tooling.
- Canonical schema limitations must be discovered during the Phase 0 fidelity proof.
- Runtime code consumes generated or mechanically derived validators rather than redefining payloads.
