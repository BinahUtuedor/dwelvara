# Dwelvara Intelligence Layer Architecture

## Purpose

The Dwelvara Intelligence Layer is the Python-based platform that turns marketplace data and approved external data into signals, predictions, recommendations, explanations and agent-assisted experiences.

It is implemented within:

```text
apps/python-intelligence
```

It does not replace the Node.js marketplace backend. Node.js remains authoritative for marketplace state, authentication, permissions, transactional workflows, persistence and consequential product actions.

## Core Principle

> Machine-learning models predict. Intelligence services interpret and combine evidence. AI agents orchestrate approved intelligence services and tools. Node.js owns authoritative marketplace workflows.

```text
Marketplace / approved external data
                 │
                 ▼
      Rules / Analytics / ML / LLMs
                 │
                 ▼
          Signals + Predictions
                 │
                 ▼
          Intelligence Services
                 │
                 ▼
         AI Agents where useful
                 │
                 ▼
           Node.js Marketplace
```

## Responsibility Layers

### Rules

Rules provide deterministic, testable signals such as verification completeness, location consistency, listing completeness, known duplicate identifiers and price-threshold checks.

### Analytics

Analytics provides descriptive and diagnostic intelligence such as median asking price, price distributions, supply/demand by locality, time on market, conversion, trends and property comparables.

Analytics must preserve the distinction between asking-price data and verified transaction-price data.

### Machine-Learning Models

ML models produce narrow predictions, probabilities, scores or rankings.

Examples:

```text
Fraud probability
Duplicate-property probability
Recommendation ranking
Price anomaly score
Future automated valuation
```

ML predictions are inputs to intelligence; they are not automatically marketplace decisions.

A future AVM may output an estimated value, uncertainty interval, model version and prediction timestamp. Property Intelligence can combine that with comparables, asking-price history, location statistics and other evidence.

### LLM Capabilities

LLMs may support listing-description assistance, property summaries, natural-language explanations, query interpretation and approved agent workflows.

LLMs must not invent property facts or turn uncertain derived information into asserted facts. Generation should be grounded in approved marketplace facts and intelligence outputs.

### Intelligence Services

The core service domains are:

```text
Trust Intelligence
Risk Intelligence
Property Intelligence
Pricing Intelligence
Investment Intelligence
Neighbourhood Intelligence
Market Intelligence
Recommendation Intelligence
```

Trust Intelligence may combine verification facts, evidence completeness, duplicate signals, risk scores, behavioural signals and listing history.

Risk Intelligence may combine fraud-model predictions, anomaly signals, duplicate analysis, account behaviour, price anomalies and rule-based warnings.

Property Intelligence may combine property facts, listing history, comparables, market statistics, geospatial context, future AVM predictions and trust/risk signals.


Pricing Intelligence may combine asking-price history, verified transaction data where available, comparables, market statistics, property characteristics, geospatial context and future AVM outputs. It must preserve the distinction between asking prices, verified transaction prices and model estimates.

Investment Intelligence may combine pricing intelligence with rental estimates, estimated yield, demand, market trends, neighbourhood context and trust/risk signals. Outputs are decision support and should expose assumptions and uncertainty.

Neighbourhood Intelligence may combine approved geospatial and external data about flood/environmental exposure, road accessibility, transport, infrastructure, utilities where reliable, schools, healthcare, amenities, development signals, local supply/demand and price/rental trends. It must preserve provenance and distinguish facts, derived measures and subjective assessments.

Market Intelligence may combine supply, demand, asking-price distributions, time on market, geographic patterns and trends.

Recommendation Intelligence may combine user intent, search behaviour, saved listings, property eligibility, trust constraints, ranking models and marketplace rules.

## Evidence-Backed Property Evaluation

The intelligence layer should be able to assemble a property evaluation from governed services, potentially including verification, document status, estimated value, asking-price assessment, comparables, area trends, rental estimate, yield, neighbourhood context, risk indicators and confidence/evidence completeness. Missing evidence must remain visibly missing or uncertain.

A future property intelligence agent may answer questions such as "Is this property a good investment?" by orchestrating these services. The response must be grounded in available evidence and expose material assumptions, provenance and uncertainty.

## AI Agents

AI agents are orchestration components above approved tools and intelligence services.

Potential future agents include:

```text
Property assistant
Buyer assistant
Agent copilot
Verification assistant
Market intelligence agent
```

An agent may interpret an objective, call approved services and assemble a response.

Agents must not write directly to marketplace tables, bypass Node.js authentication or permissions, silently approve/reject verification, invent property facts, treat LLM output as verified evidence, or replace an ML model merely because an LLM can produce a plausible answer.

## Prediction vs Intelligence vs Agent

```text
Prediction
→ narrow model output
→ "Fraud probability = 0.83"

Intelligence
→ combined interpretation
→ "High risk due to fraud score, duplicate imagery and account behaviour"

Agent
→ task-oriented orchestration
→ "Explain the risk evidence and retrieve relevant listing history"
```

## Node.js Integration

```text
User / marketplace event
        ↓
Node.js
        ↓
versioned HTTP contract or Redis job
        ↓
Python Intelligence Platform
        ↓
signals / predictions / intelligence
        ↓
Node.js
        ↓
authoritative workflow / persistence / user response
```

Python may persist derived intelligence in the `intelligence` schema but must not directly mutate authoritative `marketplace` tables.

## Database Ownership

```text
PostgreSQL
├── marketplace
│   └── Node.js / Prisma
└── intelligence
    └── Python / Alembic
```

Python consumes marketplace data only through approved versioned service contracts or approved read-only PostgreSQL views.

## Provenance, Confidence and Explainability

Where appropriate, persisted or user-facing intelligence should record source type, source identifiers, rule/model version, prompt/template version where relevant, timestamp, confidence/probability, uncertainty interval and an input-data reference.

User interfaces should distinguish:

```text
Marketplace fact
Human verification decision
Derived statistic
Rule-based signal
ML prediction
LLM-generated explanation
```

## Failure Isolation

The marketplace must continue operating when intelligence services are unavailable.

```text
Recommendations → hide or use a safe fallback
Trust score → use last available derived value where appropriate
AI summary → temporarily unavailable
Background risk processing → retry later
AVM → unavailable rather than fabricated
```

Core marketplace CRUD and transactional workflows must not require live Python availability.

## Multi-Market Design

The intelligence layer must remain market-aware rather than Lagos-hard-coded.

Markets such as `NG-LAGOS`, `NG-ABUJA` and `GB-LONDON` may differ in currency, address structure, measurement conventions, data availability, market distributions and model calibration.

A model trained for Lagos must not silently be treated as valid for London.

## Security and Safety

Use the established internal service authentication model. Apply least privilege to tools available to runtime agents. Sensitive verification evidence must not be exposed to models or agents unless required and authorised. High-impact actions remain deterministic, permission-controlled and human-reviewable where appropriate.

## Suggested Internal Structure

```text
apps/python-intelligence/
├── api/
├── intelligence/
│   ├── trust/
│   ├── risk/
│   ├── property/
│   ├── market/
│   └── recommendations/
├── models/
│   ├── fraud/
│   ├── duplicates/
│   ├── recommendations/
│   └── valuation/
├── agents/
│   ├── property-assistant/
│   ├── buyer-assistant/
│   ├── agent-copilot/
│   ├── verification-assistant/
│   └── market-intelligence/
├── analytics/
├── rules/
├── workers/
└── contracts/
```

This is a logical target structure, not a requirement to create every directory during Phase 0.

## Roadmap Alignment

The intelligence layer does not move advanced ML or agentic functionality into Phase 0.

```text
Phase 0 → intelligence platform foundation only
Phase 4 → initial trust scoring and basic duplicate detection
Phase 6 → initial AI capabilities
Phase 7 → analytics and risk capabilities
Future → advanced recommendations, fraud ML, AVM and richer agentic intelligence
```

## Architectural Guardrails

1. ML models remain explicit components; agents do not replace them.
2. Intelligence outputs are derived state, not authoritative marketplace state.
3. AI agents orchestrate approved services/tools rather than bypassing them.
4. Node.js owns consequential marketplace workflows.
5. Python does not become a duplicate CRUD backend.
6. LLM output is not treated as verified fact.
7. Predictions expose appropriate uncertainty and provenance.
8. Intelligence remains market-aware.
9. Python failure must not stop the core marketplace.
10. Do not introduce unnecessary microservices or infrastructure merely to support the intelligence layer.
