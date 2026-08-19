# Dwelvara — Repository Agent Instructions

This file contains repository-wide instructions for all coding agents working on Dwelvara, including OpenAI Codex and Claude Code.

## Source of Truth

Before making changes, read:

1. `docs/product/product-specification.md`
2. relevant files under `docs/architecture/`
3. applicable ADRs under `docs/architecture/decisions/`
4. the active implementation plan under `docs/plans/`

These documents are authoritative.

Do not silently change product requirements or architecture.

If instructions conflict, use this precedence:

```text
Product specification
        ↓
Accepted ADRs
        ↓
Active implementation plan
        ↓
AGENTS.md
        ↓
Tool-specific instructions
```

## Repository

The authoritative repository name is:

```text
dwelvara
```

Dwelvara is a monorepo from day one.

Primary applications:

```text
apps/web
→ Next.js
→ React
→ TypeScript
→ Node.js transactional marketplace backend

apps/python-intelligence
→ Python
→ FastAPI
→ workers
→ AI
→ analytics
→ trust/risk
→ property intelligence
```

Shared platform packages include:

```text
packages/contracts
packages/config
packages/database
packages/testing
```

Do not create separate repositories for the Node.js and Python applications during the MVP.

## Runtime Ownership

### Node.js / TypeScript

Node.js owns authoritative marketplace state and transactional workflows.

Use Node.js when functionality primarily:

- changes authoritative marketplace state;
- performs CRUD;
- handles authenticated user workflows;
- enforces RBAC or permissions;
- controls lifecycle transitions;
- handles payments, subscriptions or notifications;
- requires transactional consistency.

Node.js owns:

- authentication;
- users;
- agents;
- developers;
- markets;
- geography;
- properties;
- listings;
- verification workflows;
- search;
- enquiries;
- viewings;
- saved listings;
- moderation;
- payments;
- subscriptions;
- administration;
- audit logging;
- marketplace event capture.

### Python

Python owns the Dwelvara Intelligence Platform: derived intelligence and computational workloads.

Use Python when functionality primarily performs:

- AI;
- trust/risk scoring;
- fraud/anomaly detection;
- duplicate detection;
- recommendations;
- analytics/statistics;
- advanced geospatial analysis;
- data engineering;
- machine learning;
- future valuation modelling.

Python must not become a second marketplace CRUD backend.

## Intelligence Layer Responsibilities

Treat `apps/python-intelligence` as a first-class intelligence platform.

```text
Rules
→ deterministic signals and checks

Analytics
→ descriptive/diagnostic statistics and aggregations

ML models
→ predictions, probabilities, scores and rankings

LLM capabilities
→ generation, summarisation and language reasoning over approved context

Intelligence services
→ combine rules, analytics, model outputs and marketplace facts into usable intelligence

AI agents
→ orchestrate approved tools and intelligence services for higher-level tasks
```

Machine-learning models are not replaced by the intelligence layer or by AI agents. They are components inside it.

AI agents must not directly mutate authoritative marketplace state, bypass Node.js authentication/permissions or shared contracts, invent property facts, silently change verification/moderation state, or treat model/LLM output as verified fact.

Consequential marketplace actions remain under Node.js workflow ownership and, where appropriate, human review.

Where practical, derived intelligence should retain provenance such as source type, rule/model version, timestamp, confidence or uncertainty.

Read `docs/architecture/intelligence-layer.md` before substantial AI, ML, analytics, scoring, recommendation or runtime-agent work.


### Hybrid Workflows

For hybrid functionality:

```text
Node.js
→ authentication
→ permission checking
→ orchestration
→ authoritative persistence

Python
→ computation
→ analysis
→ AI
→ derived intelligence
```

## Database Ownership

Use one PostgreSQL database with two explicit schemas:

```text
PostgreSQL
├── marketplace
│   └── Node.js / Prisma
└── intelligence
    └── Python / Alembic
```

Rules:

- Prisma is the migration authority for `marketplace`.
- Alembic is the migration authority for `intelligence`.
- Python must not redefine, migrate or directly mutate marketplace tables.
- Node.js must not migrate Python-owned intelligence tables.
- Python may read marketplace data only through versioned service contracts or approved read-only PostgreSQL views.
- Python receives SELECT-only access to approved marketplace views.
- Python receives required read/write access only to `intelligence`.
- Cross-schema writes outside these rules require an accepted ADR.
- PostGIS is enabled from Phase 0.

## Shared Contracts

`packages/contracts` is the language-neutral source of truth for Node.js/Python communication.

Use canonical JSON Schema/OpenAPI definitions.

Prefer generated or mechanically derived runtime models:

```text
Canonical JSON Schema/OpenAPI
        ↓
TypeScript → Zod
Python     → Pydantic
```

Do not independently invent equivalent payloads in the two runtimes.

Every cross-runtime HTTP or job payload must carry a schema version.

Breaking changes require a new major contract version.

Contract tests must prove that TypeScript and Python agree on canonical examples.

## Node.js ↔ Python Communication

### Synchronous

Use HTTP when a user requires an immediate intelligence result.

Centralise Node.js communication under:

`apps/web/src/infrastructure/python-client/`

Protected Python endpoints use an internal service credential.

`/health` may remain unauthenticated for health checks.

### Asynchronous

Redis is the initial asynchronous transport.

Do not assume BullMQ, Celery or another framework's private queue format is cross-language compatible.

Use a versioned language-neutral JSON job envelope containing at least:

```text
schemaVersion
jobId
jobType
occurredAt
correlationId
attempt
payload
```

Jobs must support:

- idempotency;
- retries;
- failure/dead-letter handling;
- correlation IDs;
- observable status;
- explicit result ownership.

Do not introduce Kafka during the MVP without demonstrated need.

## Core Domain Rules

### Property and Listing

Property and Listing are separate entities.

A Property represents the physical asset.

A Listing represents an offer to sell or rent that Property.

One Property may have multiple Listings over time.

### Market and Geography

Lagos is the first market, not the architecture.

Do not hard-code into generic marketplace logic:

```text
Lagos
Nigeria
NGN
+234
SQM
State
LGA
```

The architecture must support at least:

```text
NG-LAGOS
NG-ABUJA
GB-LONDON
```

without redesigning core marketplace domains.

### Address

Address is the canonical structured Property location record.

Do not maintain competing geography sources of truth on Property and Address.

### Verification

Verification is a domain, not a Boolean.

Agent verification and Property verification are separate concepts.

Python may produce risk signals.

Node.js/human workflows own verification and moderation decisions.

### Money and Measurements

Store monetary values as amount + ISO currency code.

Store measurements as value + explicit unit.

Do not assume NGN or metric units globally.

### Asking Price vs Transaction Price

Asking price is not verified transaction price.

Never treat asking-price data as completed transaction data.

## Failure Isolation

Core marketplace functionality must remain operational when Python intelligence is unavailable.

Python-dependent features should degrade gracefully.

Never make core transactional flows depend on live Python availability.

## Security

Always:

- enforce authorisation server-side;
- validate external input;
- apply least privilege;
- protect internal Python endpoints;
- keep verification evidence private by default;
- keep secrets out of source control;
- validate uploads;
- audit privileged actions;
- avoid exposing stack traces or raw database errors.

Never rely solely on frontend controls for security.

## Development Standards

### TypeScript

- Use strict mode.
- Avoid unjustified `any`.
- Use explicit domain types.
- Validate external input with Zod.
- Keep business logic out of React components.

### Python

- Use Python 3.12+ as pinned by the project.
- Use modern type hints.
- Validate API contracts with Pydantic.
- Use pytest.
- Keep domain logic out of FastAPI route handlers.
- Add heavy ML/data dependencies only when justified.

### General

- Prefer readable, maintainable code.
- Add explanatory comments/docstrings where useful.
- Write tests with each complete increment.
- Use migrations.
- Never commit secrets.
- Keep `.env.example` current.
- Keep documentation current.
- Run lint, typecheck, tests and relevant builds before declaring completion.
- Do not introduce unnecessary infrastructure.

## Phase Discipline

Before starting work, identify the active implementation phase.

Do not implement later-phase functionality unless explicitly requested.

Phase 0 is foundation only and must not implement marketplace features such as:

```text
Property
Listing
Agent
Developer
Verification
Search
Enquiry
Viewing
```

## Working Procedure

For substantial work:

1. read the relevant documentation;
2. inspect the existing implementation;
3. state the objective;
4. propose a plan;
5. identify affected files;
6. identify architecture implications;
7. implement the smallest complete increment;
8. run the required checks;
9. review the diff;
10. summarise changes;
11. record unresolved issues;
12. stop.

Do not automatically begin the next milestone.

## Multi-Agent Rule

Dwelvara may use Claude Code and OpenAI Codex.

For a given feature branch or working tree:

- nominate one primary implementation agent;
- optionally nominate the other as review agent;
- do not allow both agents to edit the same working tree concurrently.

Parallel agent work requires separate Git branches or worktrees with clearly separated scopes.

Neither agent may silently change:

- Node.js/Python ownership;
- database schema ownership;
- Property/Listing separation;
- Market/Geography architecture;
- Verification architecture;
- cross-runtime contract strategy.

Architecture changes require explicit approval and an ADR where appropriate.

See:

`docs/development/ai-agent-workflow.md`
