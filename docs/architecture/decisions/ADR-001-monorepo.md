# ADR-001: Monorepo and independent deployment units

- **Status:** Accepted
- **Date:** 2026-08-21

## Context

Dwelvara combines an authoritative Node.js marketplace with a Python intelligence platform. Shared contracts and coordinated changes benefit from one source-controlled repository, while runtime failures, builds, and deployments must remain isolated. Retrofitting a second runtime into a repository later would add avoidable coordination and contract drift.

## Decision

Use one authoritative repository named `dwelvara` from day one. Place the independently deployable applications at `apps/web` and `apps/python-intelligence`, with shared platform assets under `packages`, infrastructure at repository root, and common documentation and CI.

Use pnpm workspaces and Turborepo for the TypeScript workspace and task orchestration. Use Node.js 24 LTS and pin pnpm plus compatible stable framework/infrastructure versions selected and recorded during implementation. Python remains Python 3.12 and uses `uv`, `pyproject.toml`, and `uv.lock`; Python dependency management is not forced into pnpm.

The web and Python application artifacts must build and deploy independently. A change to one runtime must not inherently require rebuilding the other.

## Alternatives considered

- Separate Node.js and Python repositories: rejected because it increases contract drift and coordination during the MVP.
- A single combined runtime/artifact: rejected because it couples failure and deployment boundaries.
- npm or Yarn workspaces: viable, but pnpm and Turborepo are already settled by the product specification.
- Managing Python through pnpm: rejected because it obscures Python-native dependency and environment management.

## Consequences

- Cross-runtime contracts, tests, and documentation can change atomically.
- CI must preserve separate build graphs and artifacts.
- Repository tooling must understand both ecosystems without pretending they share a package manager.
- Parallel coding-agent edits require separate branches/worktrees and non-overlapping scopes.
