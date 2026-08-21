# ADR-002: Node.js and Python runtime ownership

- **Status:** Accepted
- **Date:** 2026-08-21

## Context

Dwelvara needs transactional marketplace workflows and a first-class intelligence platform. Without a permanent ownership boundary, both runtimes could implement competing CRUD, permissions, or business rules, weakening consistency and security.

## Decision

`apps/web` uses Next.js, React, TypeScript, and Node.js 24 LTS. Node.js owns authentication, permissions, authoritative marketplace and future transaction state, CRUD, lifecycle transitions, orchestration, and consequential persistence.

`apps/python-intelligence` uses Python 3.12 and FastAPI. It is the Dwelvara Intelligence Platform and owns derived intelligence and computational workloads: rules, analytics, ML models, LLM capabilities, intelligence services, workers, and eventual runtime AI agents.

ML models produce predictions, probabilities, scores, and rankings. Intelligence services combine model outputs with rules, analytics, and approved marketplace facts. Runtime AI agents may orchestrate approved services and tools; they do not replace ML models, bypass contracts or Node.js permissions, mutate authoritative marketplace state, invent property facts, or make silent verification/moderation decisions.

Hybrid workflows authenticate, authorise, orchestrate, and persist authoritative outcomes in Node.js; Python computes and returns derived results. Core marketplace operation must not require live Python availability.

## Alternatives considered

- Put all backend work in Node.js: rejected because it weakens the Python-native intelligence and ML platform.
- Make FastAPI a second marketplace API: rejected because duplicate authority creates inconsistent state and permissions.
- Let Python execute consequential decisions: rejected because model/LLM outputs are derived evidence, not authoritative decisions.
- Split each capability into a microservice: rejected as unnecessary MVP complexity.

## Consequences

- Every capability has one authoritative runtime.
- Python-dependent features require graceful fallback or delayed processing.
- Cross-runtime calls use shared versioned contracts.
- Future intelligence can evolve without rewriting marketplace CRUD.
- Phase 0 creates only infrastructure seams; it implements no marketplace or intelligence features.
