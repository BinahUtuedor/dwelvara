# Dwelvara

Dwelvara is a Lagos-first trusted property marketplace and intelligence platform designed to help people discover, verify, evaluate and ultimately transact property with confidence.

Lagos is the first configured market, not the architectural boundary. The platform is designed to support additional Nigerian cities, the United Kingdom and other markets without redesigning its core marketplace domains.

## Product Direction

Dwelvara does not aim to compete primarily on listing volume.

Its differentiation is built around:

```text
Property discovery
        +
Verified property professionals
        +
Property verification
        +
Trust and reputation
        +
Marketplace data
        +
Property intelligence
```

The long-term customer journey is:

```text
DISCOVER → VERIFY → EVALUATE → TRANSACT → OWNERSHIP CONFIDENCE
```

The MVP focuses on the marketplace, supply, trust, engagement and initial intelligence foundations. Full transaction enablement is a future strategic direction rather than an MVP requirement.

## Intelligence Platform

Dwelvara treats intelligence as a first-class platform capability.

```text
Marketplace + approved external data
        ↓
Rules / Analytics / ML Models / LLM Capabilities
        ↓
Signals + Predictions
        ↓
Intelligence Services
        ↓
AI Agents where useful
        ↓
Marketplace experiences and workflows
```

Core intelligence domains include:

- Trust Intelligence
- Risk Intelligence
- Property Intelligence
- Pricing Intelligence
- Investment Intelligence
- Neighbourhood Intelligence
- Market Intelligence
- Recommendation Intelligence

Machine-learning models remain explicit components of the intelligence platform. AI agents orchestrate approved tools and intelligence services; they do not replace ML models or authoritative marketplace workflows.

Over time, governed property, listing, verification, demand, market, geospatial, neighbourhood, valuation, verified transaction and, where lawful, ownership-outcome data can create a compounding data advantage that improves recommendations, risk detection, comparables, market insight and future valuation.

## Architecture

Dwelvara uses one monorepo with two independently deployable application runtimes:

```text
dwelvara/
├── apps/
│   ├── web/                    # Next.js / React / TypeScript / Node.js marketplace
│   └── python-intelligence/    # FastAPI / Python / workers / intelligence
├── packages/
│   ├── contracts/
│   ├── config/
│   ├── database/
│   └── testing/
├── infrastructure/
├── docs/
├── scripts/
├── AGENTS.md
├── CLAUDE.md
└── README.md
```

The permanent runtime rule is:

> **Node.js runs the marketplace. Python makes the marketplace intelligent.**

Node.js/TypeScript owns authoritative marketplace and future transaction state. Python owns derived intelligence and computational workloads. The core marketplace must continue operating if Python intelligence is unavailable.

## Data and Runtime Boundaries

The operational PostgreSQL database uses explicit schema ownership:

```text
PostgreSQL
├── marketplace
│   └── Node.js / Prisma
└── intelligence
    └── Python / Alembic
```

Cross-runtime communication uses versioned, language-neutral contracts. Redis is the initial asynchronous transport. PostGIS supports geospatial capabilities.

## Multi-Market Design

Market-specific assumptions such as Lagos, Nigeria, NGN, address terminology and measurement units must remain configuration or reference data rather than generic application logic.

The architecture should support markets such as:

```text
NG-LAGOS
NG-ABUJA
NG-PORT-HARCOURT
GB-LONDON
GB-MANCHESTER
```

without redesigning the core marketplace.

## Documentation

The authoritative product and technical specification is:

`docs/product/product-specification.md`

Important supporting documents include:

- `docs/architecture/architecture.md`
- `docs/architecture/intelligence-layer.md`
- `docs/architecture/decisions/`
- `docs/plans/`
- `AGENTS.md`
- `CLAUDE.md`

Product requirements and accepted architectural decisions are the source of truth. Coding agents such as OpenAI Codex and Claude Code are implementation tools, not sources of truth.
