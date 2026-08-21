# ADR-004: Database-backed market and geography architecture

- **Status:** Accepted
- **Date:** 2026-08-21

## Context

Lagos is Dwelvara's first production market, but future Nigerian and UK markets differ in geography, currency, locale, time zone, phone conventions, measurement units, verification rules, and data availability. Hard-coded Lagos or Nigerian assumptions would require redesign.

## Decision

Market and Geography are first-class, authoritative Node.js domains backed by the marketplace database. Runtime market configuration is database-backed. Lagos is the first configured market, not an architectural boundary.

The architecture must support at least `NG-LAGOS`, `NG-ABUJA`, and `GB-LONDON` without redesigning core marketplace domains. Currency uses amount plus ISO code; measurements use value plus explicit unit. Country-specific administrative levels and postal codes are optional/configurable. Address is the canonical structured Property location record.

Python consumes market/geography facts only through versioned service contracts or approved versioned read-only views and must remain market-aware. A model calibrated for one market cannot silently apply to another.

Phase 0 may define configuration and contract seams but must not implement Market, Geography, Address, or Lagos seed data.

## Alternatives considered

- Hard-code Lagos/Nigeria/NGN/SQM: rejected because expansion would require domain redesign.
- Store market behaviour only in source files: rejected because runtime configuration is settled as database-backed.
- Give Python an independent geography source of truth: rejected because it would duplicate authoritative state.

## Consequences

- Later domain records and intelligence inputs carry explicit market context.
- Adding a market becomes reference-data/configuration work rather than a new subsystem.
- Market-specific validation and model calibration remain explicit.
- Phase 1, not Phase 0, implements the authoritative market/geography data.
