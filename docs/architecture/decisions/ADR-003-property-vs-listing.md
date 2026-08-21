# ADR-003: Property and Listing are separate domain concepts

- **Status:** Accepted
- **Date:** 2026-08-21

## Context

A physical property persists while offers to sell or rent it change over time. Combining the asset and offer would lose listing history, confuse asking prices with transaction outcomes, and obstruct future comparables and intelligence.

## Decision

Model Property as the persistent physical asset and Listing as a commercial offer associated with that Property. One Property may have multiple Listings over time and may move between sale and rent offers.

Property does not own offer-specific data such as asking price, transaction type, listing party, publication state, or expiry. Listing asking prices remain distinct from verified completed transaction prices. Address is the canonical structured location record associated with Property rather than a competing set of location fields on Property.

Node.js owns both authoritative domains and their lifecycle rules. Python may later consume approved facts and produce derived intelligence but cannot mutate them.

This ADR documents a future domain boundary only; Phase 0 must not create Property, Listing, Address, or transaction tables/contracts/functionality.

## Alternatives considered

- One combined PropertyListing entity: rejected because asset facts and offer facts have different lifecycles.
- Store current price/status directly on Property: rejected because it destroys historical and multi-offer semantics.
- Let Python define analytical copies as authoritative entities: rejected because marketplace state belongs to Node.js.

## Consequences

- Later schemas and APIs must use explicit Property and Listing identifiers and relationships.
- Listing and asking-price history can be preserved without duplicating the physical asset.
- Future transaction prices require separately governed records and verification.
- Phase 0 contains no implementation of this domain.
