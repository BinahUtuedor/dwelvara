# Phase 0 — Architecture and Monorepo Foundation Implementation Plan

**Status:** Approved for implementation; execute one reviewed step at a time

**Phase:** 0 — foundation only

**Repository:** `dwelvara`
**Authoritative inputs reviewed:** `AGENTS.md`, `README.md`, product specification v1.4, intelligence-layer architecture, AI-agent workflow. No accepted ADRs or earlier Phase 0 plan existed when this plan was written.

## 1. Objective, scope, and guardrails

Phase 0 creates one monorepo containing two independently buildable and deployable runtimes, explicit database ownership, language-neutral contracts, authenticated synchronous communication, reliable asynchronous communication, local orchestration, tests, and CI. It proves architectural seams without creating product behaviour.

The permanent boundary is:

```text
apps/web (Node.js/TypeScript)          apps/python-intelligence (Python)
authoritative marketplace backend     derived intelligence/computation
             │                                      │
             └── versioned HTTP / Redis contracts ──┘
```

Phase 0 may create only health/status behaviour, contract proof behaviour, migration metadata, and minimal proof records needed for infrastructure verification. It must not create Property, Listing, Agent, Developer, Verification, Search, Enquiry, Viewing, trust scoring, fraud/recommendation/valuation models, neighbourhood or investment intelligence, LLM product features, or runtime AI agents. It must not create marketplace domain tables or seed market/geography entities. Runtime market configuration is ultimately database-backed beginning in the appropriate later phase; `DEFAULT_MARKET` in Phase 0 is an optional deployment default, not domain data or a hard-coded market boundary.

## 2. Target repository structure

Empty future-domain directories must not be created merely as placeholders. Each listed file has Phase 0 work.

```text
dwelvara/
├── .github/workflows/ci.yml
├── apps/
│   ├── web/
│   │   ├── src/
│   │   │   ├── app/
│   │   │   │   ├── api/health/route.ts
│   │   │   │   ├── globals.css
│   │   │   │   ├── layout.tsx
│   │   │   │   └── page.tsx
│   │   │   ├── infrastructure/
│   │   │   │   ├── database/client.ts
│   │   │   │   ├── queue/{client,producer,status}.ts
│   │   │   │   └── python-client/{client,errors,index}.ts
│   │   │   └── env.ts
│   │   ├── tests/{unit,integration,contract}/
│   │   ├── Dockerfile
│   │   ├── next.config.ts
│   │   ├── package.json
│   │   ├── postcss.config.mjs
│   │   ├── tsconfig.json
│   │   └── vitest.config.ts
│   └── python-intelligence/
│       ├── app/
│       │   ├── api/{dependencies.py,health.py,foundation.py}
│       │   ├── config/settings.py
│       │   ├── infrastructure/
│       │   │   ├── database/{base.py,session.py}
│       │   │   ├── queue/{consumer.py,redis.py}
│       │   │   └── observability/logging.py
│       │   ├── services/{foundation.py,job_results.py}
│       │   ├── workers/main.py
│       │   └── main.py
│       ├── alembic/{versions,env.py,script.py.mako}
│       ├── tests/{unit,integration,contract}/
│       ├── alembic.ini
│       ├── Dockerfile
│       └── pyproject.toml
├── packages/
│   ├── contracts/
│   │   ├── schemas/foundation/v1/{ping-request,ping-response,job-envelope,job-result}.schema.json
│   │   ├── openapi/foundation-v1.yaml
│   │   ├── examples/foundation/v1/*.json
│   │   ├── generated/typescript/*.ts
│   │   ├── generated/python/*.py
│   │   ├── scripts/{generate,check-generated}.*
│   │   ├── tests/{typescript,python}/
│   │   ├── package.json
│   │   └── README.md
│   ├── config/{src,package.json,tsconfig.json}/
│   ├── database/
│   │   ├── prisma/{schema.prisma,migrations}/
│   │   ├── sql/{bootstrap,read-models,tests}/
│   │   ├── src/{client.ts,index.ts}
│   │   ├── package.json
│   │   └── README.md
│   └── testing/{src,package.json,tsconfig.json}/
├── infrastructure/docker/postgres/init/
│   └── 001-bootstrap.sql
├── scripts/{check-db-boundaries,wait-for-services}.*
├── .dockerignore
├── .env.example
├── .gitignore
├── docker-compose.yml
├── package.json
├── pnpm-lock.yaml
├── pnpm-workspace.yaml
├── turbo.json
└── README.md
```

`packages/config` contains shareable TypeScript lint/TypeScript configuration only; it does not encode Lagos as application logic. `packages/testing` contains reusable TypeScript test helpers. Python remains managed exclusively through its `pyproject.toml` and does not become a pnpm package.

## 3. Monorepo foundation

Use pnpm workspaces with:

```yaml
packages:
  - apps/web
  - packages/*
```

Pin Node.js 24 LTS through `engines` and `.nvmrc`. Pin pnpm through root `packageManager`. At implementation time, verify and record compatible stable versions of pnpm and required frameworks/infrastructure before installation. The root `package.json` is private and has no application runtime dependencies. It exposes `dev`, `build`, `lint`, `typecheck`, `test`, `test:contracts`, `check:migrations`, and `format:check`; scripts invoke Turbo for JavaScript work and explicit `uv` commands for Python work where appropriate.

`turbo.json` declares `build` outputs (`.next/**`, excluding cache), persistent uncached `dev`, and cacheable lint/typecheck/test tasks with dependency-aware `dependsOn`. Environment variables used by builds are declared so caching is correct. The lockfile is committed.

`.gitignore` excludes Node/Python caches, virtual environments, build artifacts, coverage, local `.env*` files while retaining `.env.example`, IDE/OS debris, logs, and local database volumes. `.dockerignore` excludes the same unnecessary build context. `.env.example` documents separate URLs/credentials for bootstrap/admin, Node marketplace, Python intelligence, Redis, service authentication, application URLs, timeouts, and queue names with safe placeholders only.

README integration documents prerequisites, ownership boundaries, setup commands, local URLs, health checks, independent builds, tests, database-role rules, and the explicit Phase 0 limit.

## 4. `apps/web`

Create a Next.js App Router application with React, strict TypeScript (`strict`, `noUncheckedIndexedAccess`, and no unjustified `any`), ESLint, Tailwind, and Vitest. Business logic stays outside React components.

The root page is a restrained infrastructure status page. It may show the web process and links/instructions for the two health endpoints; server-side optional probes may show PostgreSQL, Redis, and Python as `healthy`, `degraded`, or `not checked`. A failed Python probe must render a degraded state without failing the page. It contains no marketplace copy flow, cards, search, authentication, or dashboards.

`GET /api/health` returns a stable, non-sensitive JSON response and HTTP 200 when the Node process is operational. Its default liveness response must not depend on Python. An optional readiness mode/probe reports PostgreSQL and Redis separately without exposing stack traces, credentials, or raw database errors.

Use Zod in `src/env.ts` to validate server-only environment variables once. Never expose database, Redis, or internal-service secrets through `NEXT_PUBLIC_*`. Database access is centralised in `packages/database`; a development global singleton prevents excess Prisma clients. Redis access is centralised and lazily initialised so module import and web startup do not require Redis/Python availability.

All FastAPI calls go through `src/infrastructure/python-client/`. The typed client consumes generated contract validators, injects the internal credential and correlation ID, enforces deadlines, maps transport/timeout/4xx/5xx/contract failures to explicit error types, and never leaks internal bodies to public clients.

Tests are split into unit (environment, client error mapping, status presentation), integration (health, PostgreSQL/Redis adapters), and contract tests. No marketplace module directory is created in Phase 0.

## 5. `apps/python-intelligence`

Use Python 3.12, `uv` for Python dependency/environment management, a PEP 517 `pyproject.toml`, a committed `uv.lock`, a `src`-style or importable `app` package used consistently, FastAPI, Pydantic v2, pydantic-settings, SQLAlchemy 2, PostgreSQL driver, Redis client, Alembic, pytest, Ruff, and mypy. pnpm must not manage Python.

`app.main` builds FastAPI via an application factory. `GET /health` is unauthenticated, non-sensitive, and suitable for container liveness. Protected `/v1/foundation/ping` validates the Phase 0 request and response contract and proves authenticated interoperability without intelligence behaviour. `GET /v1/jobs/{job_id}` is protected and returns Python-owned Phase 0 job status/result using the canonical result contract.

Pydantic settings validate environment variables, hide secrets from representations, and use one documented naming scheme. SQLAlchemy sessions connect with the Python runtime role, set/verify `search_path=intelligence`, and use explicit schema names in metadata. Alembic imports only intelligence-owned metadata and includes a schema filter that rejects every object outside `intelligence`.

The worker is a separately startable process using the same image but a distinct command. It consumes the Phase 0 Redis stream, validates the canonical envelope, processes only `foundation.ping`, records derived proof status/results in `intelligence`, and supports shutdown, retry, stale-message recovery, and structured logs. No intelligence domain packages, model dependencies, or runtime agent directories are created yet.

Tests are divided into unit, integration, and contract suites. Route handlers remain thin; authentication, proof service, queue handling, and result access are independently testable.

## 6. PostgreSQL, PostGIS, and migration ownership

### 6.1 Schemas, roles, and bootstrap

Use one PostGIS-enabled PostgreSQL database and these non-login owner roles plus login runtime roles:

| Role | Purpose | Privileges |
|---|---|---|
| `dwelvara_bootstrap` | local/CI bootstrap only | create extension, schemas, roles/grants; never an app runtime credential |
| `marketplace_owner` | owns `marketplace` | schema owner used by controlled Prisma migrations |
| `intelligence_owner` | owns `intelligence` | schema owner used by controlled Alembic migrations |
| `web_runtime` | Node runtime | connect; required DML on `marketplace`; no `intelligence` privileges |
| `python_runtime` | API/worker | connect; required DML on `intelligence`; no marketplace table access; SELECT only on approved views |

Bootstrap SQL enables `postgis`, creates roles/schemas, revokes default `PUBLIC` schema privileges, sets role search paths, and establishes default privileges. Production provisioning uses equivalent infrastructure automation and secret management, not the committed local passwords.

Phase 0 proof objects are limited to migration bookkeeping and:

- `marketplace._prisma_migration_probe`, a trivial Prisma-owned table with an ID/timestamp and no domain meaning;
- `intelligence.foundation_job_result`, an Alembic-owned table required to prove job status/result ownership;
- `marketplace_read.v1_foundation_probe`, an approved versioned view over the Prisma proof table, in the required Phase 0 API/read-contract schema owned by `marketplace_owner`.

`marketplace_read` is an interface schema, not a third persistence owner. Python receives `USAGE` there and `SELECT` only on explicitly granted views; it receives no `USAGE` or DML grants on `marketplace`. Later views use versioned names (`v1_*`), least-field exposure, security-invoker/security-barrier settings as supported and appropriate, and contract documentation. Changed semantics require a new view version; retirement is explicit.

### 6.2 Preventing Prisma interference

Prisma's datasource sets `schemas = ["marketplace"]`; every Prisma model explicitly maps to `marketplace`; the Prisma migration connection authenticates as `marketplace_owner`; its role has no ownership or privileges in `intelligence`; and CI rejects generated migration SQL containing `intelligence` or unqualified cross-schema DDL. Prisma introspection/generation runs with schema filtering. If Prisma requires a shadow database for development, use a separate disposable database provisioned with only the marketplace schema—not the shared development database and never the production database.

These controls combine configuration, credentials, SQL review, and executable negative tests; search path alone is not a security boundary.

### 6.3 Preventing Alembic interference

Alembic authenticates as `intelligence_owner`, uses `version_table_schema="intelligence"`, sets `include_schemas=True`, and defines `include_name`/`include_object` filters that allow only `intelligence`. SQLAlchemy metadata contains only intelligence models with explicit schema. The role has no ownership/DML/DDL privilege in `marketplace`. CI renders/offline-checks revisions for forbidden schema names and performs upgrade/downgrade tests while hashing/inspecting marketplace objects before and after.

### 6.4 Migration workflow

1. Bootstrap PostGIS, roles, schemas, and grants through the privileged bootstrap script.
2. Run Prisma migrations using the marketplace migration URL, then run the Node runtime using `web_runtime`.
3. Run Alembic using the intelligence migration URL, then run API/worker with `python_runtime`.
4. Never run either migration tool with the bootstrap/superuser runtime credential.
5. Review committed migration SQL/revisions; apply them independently in CI and deployment.
6. Prove negative permissions with transactions that attempt Python INSERT/UPDATE/DDL in `marketplace` and Node DDL/DML in `intelligence`; all must fail and roll back.

## 7. Shared contracts

`packages/contracts` is the language-neutral source of truth. Canonical JSON Schema 2020-12 files define payloads; OpenAPI references them for HTTP. Each `$id` and directory carries a major version. Payloads contain `schemaVersion` (Phase 0 value `1.0`); additive optional fields may remain in v1, consumers tolerate them, and breaking changes create v2 with an explicit migration/retirement plan.

The only Phase 0 semantic contract is `foundation.ping`:

```json
{
  "schemaVersion": "1.0",
  "message": "phase-0",
  "correlationId": "00000000-0000-4000-8000-000000000001"
}
```

Its response echoes the correlation ID/message and reports a fixed `accepted` outcome. The same payload is usable by protected HTTP and as the job envelope payload. Separate generic `job-envelope` and `job-result` schemas support transport metadata/status; they are infrastructure contracts, not marketplace domain contracts.

Generation is deterministic and one-way: canonical schema/OpenAPI → generated TypeScript types/Zod validators and Python Pydantic models. Before selecting tools, implement the plan's small fidelity proof against the Phase 0 schemas and record the compatible stable tool versions selected. If a generator cannot faithfully express a constraint, a checked-in mechanical adapter may wrap generated output, but equivalent schemas must not be independently authored. Generated files carry a “do not edit” header. CI regenerates into a clean/temp location and fails on diff.

Canonical valid and invalid examples are validated by JSON Schema, Zod, and Pydantic. Cross-runtime tests assert acceptance/rejection and normalised serialisation equivalence. Contract tests also verify every HTTP/job payload declares a supported schema version.

## 8. Synchronous Node.js to Python flow

```text
Node route/service
  → central python-client
  → POST /v1/foundation/ping
  → service credential + X-Correlation-ID
  → FastAPI validation/service
  → versioned validated response
```

- Authentication: `Authorization: Bearer <INTERNAL_SERVICE_TOKEN>` for Phase 0, compared in constant time. The token is server-only, rotatable through deployment secrets, and never logged. `/health` alone is unauthenticated.
- Timeout: a configurable short total deadline (recommended default 2 seconds locally, with a bounded production value); abort the request on expiry.
- Retry: no implicit retry for arbitrary POSTs. The Phase 0 idempotent ping may retry once for connection reset/timeout/502/503/504 with jitter, within the original deadline. Never retry authentication, validation, or other 4xx failures.
- Errors: distinguish unavailable, timeout, unauthorised, upstream rejection, malformed response, and unsupported contract. Return safe degraded responses at integration boundaries and structured logs internally.
- Correlation: accept or generate a UUID at Node ingress, forward it in header and body/envelope, return it, and include it in logs/status.
- Graceful degradation: Python calls are opt-in. Web startup, `/api/health`, and the status page remain operational when Python is stopped; the UI reports intelligence unavailable rather than fabricating output.

## 9. Asynchronous Node.js to Python flow

Select Redis Streams with consumer groups. Streams are a native, cross-language Redis primitive, retain entries until explicit trimming, support acknowledgements and pending-entry recovery, and avoid coupling to BullMQ/Celery private protocols.

Use configurable keys with stable defaults:

```text
dwelvara:jobs:foundation:v1
dwelvara:jobs:foundation:v1:dlq
consumer group: python-intelligence-v1
```

The Node producer validates and `XADD`s one JSON field containing the exact canonical envelope:

```json
{
  "schemaVersion": "1.0",
  "jobId": "uuid",
  "jobType": "foundation.ping",
  "occurredAt": "ISO-8601 UTC",
  "correlationId": "uuid",
  "attempt": 1,
  "payload": {"schemaVersion":"1.0","message":"phase-0","correlationId":"uuid"}
}
```

The Python worker uses `XREADGROUP`, validates before dispatch, and acknowledges only after the database transaction commits. `jobId` is unique in `intelligence.foundation_job_result`; insert/upsert semantics make re-delivery idempotent. Status progresses `queued` (observable initially from Redis/producer response), `processing`, then `succeeded`, `retrying`, or `failed`. Python owns persisted result/status because it owns computation. Node retains only the submitted `jobId` in Phase 0 and reads results through Python, not the intelligence schema.

Retryable failures are re-enqueued with the same `jobId`, incremented `attempt`, preserved correlation ID, and bounded exponential backoff implemented by a small retry scheduler using a sorted set or explicit delayed-retry key. Recommend three total attempts. A worker periodically uses `XAUTOCLAIM` to recover stale pending entries. Validation errors and exhausted jobs are copied to the DLQ with failure category/timestamp and then acknowledged from the source stream. DLQ entries never contain secrets or stack traces and are not automatically replayed.

Observability includes structured logs keyed by `jobId`, `correlationId`, `jobType`, attempt, stream entry ID, duration, and outcome; counters for produced/processed/retried/failed; pending count, oldest pending age, DLQ depth, and processing latency. Stream retention uses approximate length/time trimming only after acknowledged/persisted results make removal safe.

## 10. Async job result access

The settled flow is:

```text
Node submits envelope → Redis Stream → Python worker
    → Python writes derived status/result to intelligence.foundation_job_result
    → Node calls protected GET /v1/jobs/{jobId} through python-client
    → Python authorises, retrieves, validates, and returns canonical job-result
    → Node presents/uses the result without reading intelligence tables
```

Unknown IDs return 404; unfinished jobs return the canonical nonterminal status; failures return a safe code/message; successful results include the versioned foundation response. The endpoint verifies UUID format and service credentials and does not expose arbitrary lookup/query capability. Polling uses bounded exponential backoff and a caller deadline. Later push/event mechanisms require a real need and an ADR.

## 11. Docker Compose

One root Compose file defines `postgres` (a pinned PostGIS image), `redis` (pinned Redis image with append-only persistence for useful local durability), `web`, `python-intelligence-api`, and `python-intelligence-worker`. The requested logical `python-intelligence` application is represented by API and worker processes built once from the same independently deployable image.

- Network: one private application network; only development-facing ports are optionally published and configurable.
- Volumes: named PostgreSQL and Redis data volumes; source bind mounts may be in a development override, not the production-like base.
- Health checks: `pg_isready`, `redis-cli ping`, HTTP checks for `/api/health` and `/health`. The worker has a process/heartbeat health check that does not claim workload success.
- Startup: PostgreSQL bootstrap completes first; migration one-shot services run with owner credentials; apps wait on healthy infrastructure/migration completion. Compose ordering improves local startup but applications still use bounded connection retries.
- Failure isolation: web does not depend on Python API/worker being healthy. It may depend on PostgreSQL/Redis readiness for integration mode, while its liveness endpoint remains process-focused.
- Environment: interpolate `.env`; use distinct migration/runtime URLs and internal container URLs. No secrets are baked into images.
- Images: multi-stage web and Python Dockerfiles run as non-root users, have explicit commands, copy only needed artifacts, and can be built independently.

## 12. Testing strategy

| Test | Proof |
|---|---|
| Node unit | environment validation, Python-client errors/retry/deadline, queue envelope, status rendering |
| Python unit | settings, auth, services, envelope dispatch, retry classification, result mapping |
| Database integration | PostGIS query, both runtime connections, proof objects |
| Contract | canonical valid/invalid examples agree in JSON Schema, Zod, and Pydantic; generated files current |
| Migration isolation | Prisma changes only marketplace; Alembic changes only intelligence; forbidden runtime writes fail |
| Cross-runtime HTTP | Node central client successfully calls authenticated foundation ping; missing/bad token rejected |
| Cross-runtime Redis | Node produces one v1 envelope; Python consumes exactly/idempotently and persists result |
| Job-result endpoint | protected lookup returns processing/succeeded/failed shapes and rejects unauthorised/unknown IDs |
| Retry/DLQ | transient failure retries with incremented attempt; poison/exhausted job reaches DLQ |
| Failure isolation | stop/unroute Python; web starts, `/api/health` and status foundation still respond, Python feature is degraded |
| Docker integration | clean Compose start, health convergence, HTTP and job smoke tests, independent service restart |
| Independent artifacts | build/run web image alone and Python image as API and worker commands |

Integration tests use isolated CI databases/Redis namespaces and unique IDs. They must not depend on execution order. Migration tests inspect catalogs before/after as well as expecting permission errors.

## 13. CI design

Create only `.github/workflows/ci.yml`. Trigger on pull requests and pushes to protected branches. Use concurrency cancellation and least GitHub permissions. Jobs run in parallel where dependencies allow:

1. `node-lint`, `node-typecheck`, `node-test`, and `node-build` restore pnpm cache and use frozen lockfiles.
2. `python-lint`, `python-typecheck`, and `python-test` use Python 3.12 and the selected reproducible install method.
3. `contract-tests` generates/checks artifacts and runs JSON Schema, Zod, and Pydantic example suites.
4. `migration-schema-checks` starts PostGIS, bootstraps roles/schemas, applies Prisma and Alembic independently, runs isolation/permission checks, and tests clean rebuilds.
5. `cross-runtime-integration` starts PostgreSQL and Redis plus both runtimes, then runs protected HTTP, stream, result endpoint, and Python-failure tests.
6. `container-builds` builds the web and Python images independently; smoke tests their health commands where practical.

Build jobs may depend on lint/typecheck; integration depends on contract and migration checks. Pin action major versions/commit SHAs per repository policy. Do not add deployment or cloud infrastructure to Phase 0 CI.

## 14. ADRs justified by settled decisions

Create these during Phase 0 implementation (not as part of this planning-only task), each with Context, Decision, Alternatives considered, and Consequences:

| Filename | Architectural question | Decision documented |
|---|---|---|
| `ADR-001-monorepo.md` | Repository and deployment topology? | One `dwelvara` monorepo; two independently deployable runtimes; pnpm/Turbo for TS only |
| `ADR-002-node-python-boundary.md` | Which runtime owns which work? | Node owns authoritative marketplace/transactions; Python owns derived intelligence/computation and never duplicate CRUD |
| `ADR-003-property-vs-listing.md` | Are physical asset and commercial offer the same entity? | They are separate; one Property may have Listings over time |
| `ADR-004-market-and-geography.md` | Is Lagos embedded in the domain model? | Database-backed Market/Geography, Lagos first configuration, Address canonical, multi-market design |
| `ADR-005-database-schema-ownership.md` | How is one PostgreSQL database safely shared? | marketplace/Prisma versus intelligence/Alembic, role isolation, approved versioned read-only views, PostGIS |
| `ADR-006-cross-runtime-contracts.md` | How do TS and Python avoid payload drift? | Canonical JSON Schema/OpenAPI, versioning, mechanical Zod/Pydantic derivation, shared examples/tests |
| `ADR-007-sync-vs-async-communication.md` | How do runtimes communicate? | Central authenticated HTTP for immediate work; Redis Streams and explicit JSON envelopes for background work; protected Python result endpoint |

No ADR is proposed for a cloud provider, authentication vendor, production deployment platform, observability vendor, object storage provider, or future intelligence implementation because none is required or approved for Phase 0.

## 15. Exact implementation sequence

Each step is a stopping/checkpoint boundary. Commands are representative PowerShell-friendly commands run from repository root; exact pinned versions are selected and recorded during implementation, not guessed by this plan.

### Step 1 — Record architecture decisions

1. **Objective:** Convert settled foundation decisions into accepted ADRs before code.
2. **Files:** `docs/architecture/decisions/ADR-001...ADR-007`.
3. **Dependencies:** None.
4. **Commands:** create/edit ADRs; `git diff --check`.
5. **Validation:** human review against product specification and this plan; all ADRs contain required sections.
6. **Previous steps:** None.
7. **Expected result:** implementation has immutable, reviewable boundaries.

### Step 2 — Scaffold root workspace

1. **Objective:** Establish pnpm/Turbo orchestration and repository hygiene.
2. **Files:** root `package.json`, `pnpm-workspace.yaml`, `turbo.json`, `.nvmrc`, `.gitignore`, `.dockerignore`, `.env.example`; empty working package roots only as subsequent steps require.
3. **Dependencies:** pnpm, Turbo, root lint/format tooling.
4. **Commands:** `corepack enable`; `pnpm install`; `pnpm exec turbo --version`.
5. **Validation:** frozen install succeeds; workspace listing is correct; no Python project is treated as pnpm workspace.
6. **Previous steps:** Step 1.
7. **Expected result:** deterministic monorepo task foundation.

### Step 3 — Create shared TypeScript support packages

1. **Objective:** Add minimal reusable configuration, database, and testing package seams.
2. **Files:** `packages/config`, `packages/database` package shell, `packages/testing`.
3. **Dependencies:** TypeScript, ESLint/config packages, Prisma client tooling, Vitest helpers.
4. **Commands:** `pnpm install`; `pnpm --filter ./packages/... typecheck`.
5. **Validation:** packages resolve through workspaces and have no domain logic.
6. **Previous steps:** Step 2.
7. **Expected result:** shareable foundation packages with explicit exports.

### Step 4 — Establish canonical contracts

1. **Objective:** Make the v1 foundation ping, job envelope, and job result the single source of cross-runtime truth.
2. **Files:** all listed `packages/contracts` schemas, OpenAPI, examples, generation scripts, generated outputs, and tests.
3. **Dependencies:** JSON Schema validator, OpenAPI/schema code generation, Zod, Pydantic generation support.
4. **Commands:** `pnpm --filter @dwelvara/contracts generate`; `pnpm --filter @dwelvara/contracts test`; Python contract test command from its documented environment.
5. **Validation:** valid/invalid fixtures agree in JSON Schema/Zod/Pydantic; regeneration yields no diff.
6. **Previous steps:** Steps 2–3.
7. **Expected result:** one versioned interoperable infrastructure contract.

### Step 5 — Bootstrap PostgreSQL ownership and PostGIS

1. **Objective:** Establish schemas, roles, grants, and extension before ORM migrations.
2. **Files:** `infrastructure/docker/postgres/init/001-bootstrap.sql`, database SQL tests, `.env.example` additions.
3. **Dependencies:** pinned PostGIS PostgreSQL image/client.
4. **Commands:** `docker compose up -d postgres`; execute boundary-check script using bootstrap test credential; query `SELECT PostGIS_Version()`.
5. **Validation:** roles and schemas exist, `PUBLIC` is revoked, runtime negative privilege tests pass.
6. **Previous steps:** Step 2.
7. **Expected result:** one database with enforceable least-privilege ownership.

### Step 6 — Prove Prisma marketplace migration

1. **Objective:** Configure Prisma to control only `marketplace` and create its non-domain proof object.
2. **Files:** `packages/database/prisma/schema.prisma`, first migration, database client/tests/README.
3. **Dependencies:** Prisma CLI/client and PostgreSQL driver adapter if required by selected Prisma version.
4. **Commands:** `pnpm --filter @dwelvara/database prisma:generate`; `pnpm --filter @dwelvara/database prisma:migrate:deploy`.
5. **Validation:** proof table and Prisma history are in marketplace only; intelligence catalog unchanged; runtime connection works.
6. **Previous steps:** Steps 3 and 5.
7. **Expected result:** Prisma migration mechanism and isolation proven without domain tables.

### Step 7 — Scaffold Python API and Alembic ownership

1. **Objective:** Create the Python 3.12 application, settings, database connection, unauthenticated health, and intelligence-only migration.
2. **Files:** `apps/python-intelligence` pyproject, app/config/database/API, Alembic files, tests, Dockerfile.
3. **Dependencies:** FastAPI, Uvicorn, Pydantic/settings, SQLAlchemy, PostgreSQL driver, Alembic, pytest, Ruff, mypy.
4. **Commands:** `uv sync --frozen`; `uv run alembic upgrade head`; `uv run ruff check .`; `uv run mypy app`; `uv run pytest`.
5. **Validation:** `/health` passes; `foundation_job_result` exists only in intelligence; marketplace catalog unchanged; downgrade/upgrade works in disposable DB.
6. **Previous steps:** Steps 4–6.
7. **Expected result:** independently runnable Python API and isolated Alembic mechanism.

### Step 8 — Scaffold Next.js foundation

1. **Objective:** Create strict web app, status page, health route, validated environment, and lazy database/Redis adapters.
2. **Files:** listed `apps/web` files and tests.
3. **Dependencies:** Next.js, React, TypeScript, Tailwind, Zod, Redis client, Vitest/testing libraries.
4. **Commands:** `pnpm --filter @dwelvara/web lint`; `typecheck`; `test`; `build`; `dev` for manual smoke test.
5. **Validation:** page and `/api/health` respond; build is standalone/deployable; no marketplace UI/modules exist.
6. **Previous steps:** Steps 2–6.
7. **Expected result:** independent Node foundation that does not require Python to start.

### Step 9 — Implement protected synchronous proof

1. **Objective:** Prove central authenticated Node-to-Python HTTP using generated contracts.
2. **Files:** Python auth dependency/foundation route/service; web python-client and tests.
3. **Dependencies:** existing HTTP APIs/runtime libraries; no new service framework.
4. **Commands:** start both apps; run cross-runtime HTTP integration tests.
5. **Validation:** valid token/payload succeeds with correlation ID; bad token, timeout, malformed response, and Python outage are safely handled.
6. **Previous steps:** Steps 4, 7, 8.
7. **Expected result:** synchronous seam works and degrades safely.

### Step 10 — Implement Redis Streams worker proof

1. **Objective:** Send one canonical v1 job from Node to Python with idempotency, recovery, retries, DLQ, and persisted result.
2. **Files:** web queue producer/status; Python queue/worker/result service; tests; Compose Redis configuration.
3. **Dependencies:** Node and Python Redis clients only.
4. **Commands:** `docker compose up -d redis postgres`; start worker; run cross-runtime queue tests.
5. **Validation:** success, duplicate delivery, stale claim, transient retry, poison message/DLQ, and correlation logging tests pass.
6. **Previous steps:** Steps 4–9.
7. **Expected result:** framework-neutral background seam is demonstrably reliable.

### Step 11 — Implement protected job-result access

1. **Objective:** Let Node observe Python-owned result without database boundary violation.
2. **Files:** Python `/v1/jobs/{job_id}` route/service; web python-client method; tests.
3. **Dependencies:** existing contracts/database/client libraries.
4. **Commands:** run endpoint and end-to-end job tests.
5. **Validation:** authorised status/result flow works; unauthorised, unknown, processing, failed cases conform; Node has no intelligence DB grant.
6. **Previous steps:** Step 10.
7. **Expected result:** settled async result flow is proven end to end.

### Step 12 — Complete Docker Compose and artifacts

1. **Objective:** Run the complete local foundation and prove independent artifacts.
2. **Files:** root Compose, both Dockerfiles, wait/health scripts, environment documentation.
3. **Dependencies:** Docker/Compose and pinned base images.
4. **Commands:** `docker compose config`; `docker compose build web python-intelligence-api`; `docker compose up --wait`; smoke tests; stop Python and repeat web health; `docker compose down` (retain volumes unless intentionally resetting test data).
5. **Validation:** all health checks converge; API/worker share an image; web remains live without Python; each image runs independently.
6. **Previous steps:** Steps 5–11.
7. **Expected result:** reproducible local stack and deployable artifacts.

### Step 13 — Add CI

1. **Objective:** Automate all quality, boundary, interoperability, and image checks.
2. **Files:** `.github/workflows/ci.yml`.
3. **Dependencies:** official setup actions and service containers.
4. **Commands:** validate workflow syntax locally where tooling exists; push branch/PR to execute CI.
5. **Validation:** every job in Section 13 passes from clean checkout with frozen dependencies.
6. **Previous steps:** Steps 2–12.
7. **Expected result:** one green, parallelised foundation workflow.

### Step 14 — Documentation and final audit

1. **Objective:** Make a first-time Codex/user workflow reproducible and confirm Phase 0 only.
2. **Files:** root/app/package READMEs, `.env.example`, this plan status/checklist only if implementation results require factual updates.
3. **Dependencies:** None.
4. **Commands:** all root lint/typecheck/test/build/contract/migration commands; Compose smoke suite; `git diff --check`; `git status --short`.
5. **Validation:** Definition of Done evidence is recorded; diff contains no feature/domain implementation or secrets; independent reviewer checks architecture.
6. **Previous steps:** All prior steps.
7. **Expected result:** reviewable Phase 0 increment; stop before Phase 1.

## 16. Phase 0 definition of done

This is the single authoritative completion checklist. Phase 1 must not begin until every item is proven.

- [ ] **Monorepo works:** clean `pnpm install --frozen-lockfile` and root Turbo task graph succeed; Python installs independently from `pyproject.toml`.
- [ ] **`apps/web` runs:** production build starts and the status page responds 200.
- [ ] **`apps/python-intelligence` runs:** API image/process starts and responds 200; worker starts separately from the same artifact.
- [ ] **PostgreSQL/PostGIS reachable:** runtime connection test succeeds and `SELECT PostGIS_Version()` returns a version.
- [ ] **Redis reachable:** both runtime clients pass a ping/integration connection test.
- [ ] **Schemas isolated:** catalog/grant assertions show marketplace and intelligence owners/runtime permissions exactly as designed.
- [ ] **Prisma migration works:** clean marketplace migration deploy creates only Prisma history/proof objects in marketplace.
- [ ] **Alembic migration works:** clean upgrade creates only Alembic history/result proof objects in intelligence; disposable downgrade/upgrade passes.
- [ ] **Node health works:** `GET /api/health` has contract-tested 200 response without Python.
- [ ] **Python health works:** unauthenticated `GET /health` has contract-tested 200 response without leaking internals.
- [ ] **Protected synchronous call works:** central client calls `/v1/foundation/ping`; correct token/correlation succeeds and invalid token is rejected.
- [ ] **Canonical contracts work:** schemas/OpenAPI validate and generated files reproduce without diff.
- [ ] **Zod/Pydantic agree:** both accept every valid canonical fixture and reject every invalid fixture with equivalent serialisation.
- [ ] **Redis job crosses Node → Python:** integration evidence shows a v1 envelope produced, claimed, processed, committed, and acknowledged.
- [ ] **Node observes Python-owned result:** protected result endpoint returns the completed canonical result for submitted `jobId`.
- [ ] **Idempotency/retry/DLQ work:** duplicate delivery has one result; transient failure retries; exhausted/invalid job is safely dead-lettered.
- [ ] **Python cannot write marketplace state:** SQL permission tests for INSERT, UPDATE, DELETE, CREATE, and ALTER fail under `python_runtime`; approved view SELECT alone succeeds.
- [ ] **Node cannot alter intelligence state:** equivalent negative DDL/DML tests fail under `web_runtime`.
- [ ] **Node tests pass:** unit and integration command exits zero with no skipped required suite.
- [ ] **Python tests pass:** pytest exits zero; Ruff and mypy pass.
- [ ] **Contract tests pass:** schema, generation freshness, Zod, Pydantic, HTTP, and job envelope suites exit zero.
- [ ] **Migration-isolation tests pass:** catalog before/after tests and forbidden-schema scans pass for both tools.
- [ ] **Docker Compose works:** clean `docker compose up --wait` reaches health and end-to-end smoke suite passes.
- [ ] **CI passes:** all jobs in the single workflow are green on a clean commit.
- [ ] **Both runtimes build independently:** web build does not build Python; Python build does not build web.
- [ ] **Both have independently deployable artifacts:** web image runs alone; Python image runs as API and worker commands without bundling web.
- [ ] **Python failure is isolated:** with Python API/worker stopped, web starts, status page and `/api/health` work, and Python state is reported as degraded.
- [ ] **Phase boundary is clean:** diff audit finds none of the explicitly forbidden Phase 0 features and no real secrets.
- [ ] **Documentation is reproducible:** a first-time user can follow README commands from clean checkout and obtain the same evidence.

## 17. Future intelligence readiness

Phase 0 creates extension points, not implementations:

- Trust and Risk Intelligence later add versioned job/HTTP payloads and Python-owned derived tables/services; Node remains the decision/workflow owner.
- Property, Pricing, Investment, Neighbourhood, and Market Intelligence later consume approved service facts or narrowly versioned read views and persist provenance-bearing derived results in intelligence.
- Recommendation Intelligence later adds eligible-input contracts and ranking outputs; Node continues to enforce eligibility and serve authoritative listings.
- ML models later live as explicit versioned components producing predictions/probabilities/scores/rankings; the established service layer combines them with rules, analytics, and facts.
- LLM capabilities later use approved, versioned contexts and record provider/model/template provenance; they cannot invent facts or write marketplace state.
- Runtime AI agents later orchestrate allow-listed intelligence services/tools behind authenticated Python interfaces; they do not replace ML models, bypass contracts, or perform consequential marketplace mutations.

Redis Streams provide a neutral worker ingress, HTTP provides immediate computation/result access, canonical contracts prevent language drift, the `intelligence` schema provides derived-state ownership, and correlation/provenance seams support observability. Market identifiers remain inputs to future contracts/configuration so a Lagos model cannot silently apply to Abuja or London. No future intelligence directory, model, agent, or feature is required in Phase 0.

## 1. DECISIONS ALREADY SETTLED

- The repository is named `dwelvara` and is a monorepo from day one.
- `apps/web` is Next.js/React/TypeScript/Node.js and owns authoritative marketplace and future transaction state.
- `apps/python-intelligence` is the Python 3.12/FastAPI Dwelvara Intelligence Platform for rules, analytics, ML, LLM capabilities, intelligence services, workers, and eventual runtime agents.
- ML models predict; intelligence services combine evidence; AI agents orchestrate. Agents do not replace models or authoritative workflows.
- One PostgreSQL/PostGIS database uses marketplace/Prisma and intelligence/Alembic ownership. Python cannot write marketplace state and reads it only through versioned services or approved read-only views.
- Cross-runtime contracts are canonical JSON Schema/OpenAPI with explicit versions and mechanically derived Zod/Pydantic validation where practical.
- Synchronous calls use the central Node Python client and protected FastAPI endpoints; `/health` may be public.
- Redis is the initial asynchronous transport with an explicit language-neutral job envelope; no BullMQ/Celery private protocol assumption and no Kafka.
- Node observes Python-owned job results through a protected Python endpoint.
- Market configuration is database-backed; Lagos is first configuration, not architecture. Property and Listing remain separate and Address is canonical location.
- Python failure must not stop core marketplace operation.
- Phase 0 is foundation only and excludes all named marketplace and intelligence features.

## 2. IMPLEMENTATION CHOICES RECOMMENDED FOR PHASE 0

- Redis Streams with consumer groups, `XAUTOCLAIM`, explicit retry scheduling, and a DLQ.
- A single `foundation.ping` proof contract reused across authenticated HTTP and the job payload, plus generic envelope/result contracts.
- Owner and runtime PostgreSQL roles, a separate `marketplace_read` interface schema, explicit schema qualification, migration SQL scanning, and negative permission tests.
- A minimal Prisma proof table and Python job-result table as the only non-metadata persistence proof objects.
- FastAPI API and worker commands built from one Python image, alongside a separately built web image.
- Vitest for Node tests; pytest/Ruff/mypy for Python; JSON Schema/Zod/Pydantic fixture parity tests.
- One parallel GitHub Actions CI workflow and one root Docker Compose stack.
- A minimal Tailwind infrastructure status page whose liveness does not depend on Python.

## 3. DECISIONS REQUIRING USER APPROVAL

No Phase 0 planning choices currently remain open. The user approved Node.js 24 LTS, Python 3.12, `uv`, the Phase 0 internal bearer credential, creation of `marketplace_read` with the foundation proof view, and selection/recording of compatible stable pnpm/framework/infrastructure and schema-generation tool versions at implementation time after the required fidelity proof. Production deployment platform and production workload identity are explicitly deferred. Any newly discovered choice that would change settled runtime, database, contract, or phase ownership requires user approval and the ADR process.

## 4. ITEMS EXPLICITLY DEFERRED BEYOND PHASE 0

- Authentication, RBAC, users, database-backed Market/Country/Geography data, Lagos seeds, storage, and audit foundation (Phase 1).
- Property, Listing, amenities, media, search, filters, property pages, and listing lifecycle (Phase 2).
- Agent/Developer registration, profiles, dashboards, property/listing management, and supply workflows (Phase 3).
- Verification workflows, trust scoring, and duplicate detection (Phase 4).
- Saved listings, enquiries, viewings, CRM, reports, and moderation (Phase 5).
- AI listing assistance and AI property summaries (Phase 6).
- Fraud/risk logic, price anomalies, and marketplace analytics (Phase 7 or later).
- Recommendation models, AVM, advanced geospatial/data engineering, and all Trust, Risk, Property, Pricing, Investment, Neighbourhood, Market, and Recommendation Intelligence implementations.
- LLM product features and runtime AI agents.
- Transaction, payment/escrow, financing, ownership-confidence, and verified transaction functionality.
- Production cloud/deployment provider, object storage provider, authentication provider integration, observability vendor, Kubernetes, Kafka, Spark, service mesh, multiple operational databases, and additional microservices.
