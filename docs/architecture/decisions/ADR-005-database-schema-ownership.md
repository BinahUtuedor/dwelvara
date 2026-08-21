# ADR-005: PostgreSQL schema and migration ownership

- **Status:** Accepted
- **Date:** 2026-08-21

## Context

Dwelvara needs transactional marketplace storage and Python-owned derived intelligence without operating multiple MVP databases. Both migration systems must coexist without either runtime redefining or mutating the other's data.

## Decision

Use one PostgreSQL database with PostGIS enabled from Phase 0 and explicit boundaries:

- `marketplace` is authoritative marketplace state, owned and migrated by Node.js/Prisma.
- `intelligence` is derived intelligence state, owned and migrated by Python/Alembic.
- `marketplace_read` is a versioned read-interface schema owned by the marketplace side, not a third persistence owner.

Use separate non-login owner roles, migration credentials, and least-privilege runtime credentials. Prisma is configured and credentialled for `marketplace` only. Alembic metadata, version table, schema filters, and credentials are limited to `intelligence`. Search paths supplement but do not replace grants and ownership boundaries.

Python receives required read/write access only in `intelligence`, no write/DDL access to `marketplace`, and `USAGE` plus explicit `SELECT` grants only for approved versioned views in `marketplace_read`. Node runtime receives no access to Python-owned intelligence state. Cross-schema writes require an accepted ADR.

Phase 0 creates `marketplace_read` and a foundation proof view to verify Python's approved read-only boundary. It may create only migration metadata, a trivial Prisma migration probe, the Python-owned job-result proof table, and that proof view—no marketplace domain tables.

CI must scan migration output, inspect catalogs before/after migrations, and execute negative permission tests. Migration tools never run with bootstrap/superuser runtime credentials.

## Alternatives considered

- Separate operational databases: rejected as unnecessary MVP operational complexity.
- One shared schema/role: rejected because tooling and runtime ownership could interfere.
- Python direct read access to all marketplace tables: rejected because it is not least privilege or a stable data contract.
- Service-contract-only access: retained as preferred for request workflows, but approved read views are also required for justified analytical/background reads.

## Consequences

- Ownership is enforced by PostgreSQL privileges as well as conventions and tooling.
- Marketplace read views require versioning, field minimisation, grants, and retirement discipline.
- Deployments use distinct bootstrap, migration, and runtime credentials.
- Integration tests must prove both positive approved reads and negative cross-boundary writes.
