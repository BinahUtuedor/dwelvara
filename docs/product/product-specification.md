# Dwelvara

## Unified Product Requirements & Technical Specification

**Version:** 1.4
**Initial launch market:** Lagos, Nigeria
**Initial country:** Nigeria
**Future markets:** Abuja, Port Harcourt, other Nigerian cities, United Kingdom, additional countries
**Implementation tooling:** Claude Code and OpenAI Codex
**Repository strategy:** Monorepo from day one
**Backend architecture:** Node.js/TypeScript + Python
**Primary proposition:** A trusted property marketplace and intelligence platform that helps people discover, verify, evaluate and ultimately transact property with confidence.

---

# 1. Product Vision

Build a trusted property marketplace and intelligence platform where buyers, renters and investors can discover genuine properties offered by verified property professionals, understand what has and has not been independently verified, evaluate properties using trustworthy intelligence and, over time, complete more of the property transaction journey with confidence.

The first production market is:

```text
Lagos, Nigeria
```

However:

> Lagos is the first configured market, not a hard-coded architectural boundary.

The same platform must support future markets such as:

```text
NG-LAGOS
NG-ABUJA
NG-PORT-HARCOURT
GB-LONDON
GB-MANCHESTER
```

without redesigning the core marketplace.

The long-term product is:

> A reusable, multi-market property intelligence, trust and transaction platform, initially deployed in Lagos.

The long-term customer journey is:

```text
DISCOVER
   ↓
VERIFY
   ↓
EVALUATE
   ↓
TRANSACT
   ↓
ESTABLISH OWNERSHIP CONFIDENCE
```

The underlying long-term capability chain is:

```text
DISCOVERY
    ↓
VERIFICATION
    ↓
PROPERTY INTELLIGENCE
    ↓
PRICING INTELLIGENCE
    ↓
NEIGHBOURHOOD INTELLIGENCE
    ↓
TRUST / FRAUD / RISK
    ↓
TRANSACTION
    ↓
OWNERSHIP
```

The simpler customer journey and the more detailed capability chain describe the same product direction at different levels. Dwelvara should progressively help a user understand not only what is advertised, but whether the property, professional, price, location context, risk and eventual transaction can be trusted.

The MVP does not implement the full transaction journey. Transaction enablement is a future strategic direction and must be introduced incrementally only when the required product, legal, operational, security and market controls are understood.

---

# 2. Core Product Proposition

The platform should not compete primarily on listing volume.

Its differentiation is:

```text
PROPERTY DISCOVERY
        +
VERIFIED PROFESSIONALS
        +
PROPERTY VERIFICATION
        +
REPUTATION
        +
LEAD MANAGEMENT
        +
MARKETPLACE DATA
        +
PROPERTY INTELLIGENCE
        +
PRICING & INVESTMENT INTELLIGENCE
        +
NEIGHBOURHOOD INTELLIGENCE
        +
TRUST / RISK INTELLIGENCE
```

The customer proposition is:

> **Discover, verify and evaluate property with evidence you can trust.**

Initial property supply should primarily come from verified:

```text
Agents
Developers
```

Private-owner listings may be introduced later once an appropriate ownership-verification process exists.


### 2.1 Strategic Data Advantage

Dwelvara should treat high-quality marketplace data as a compounding product asset rather than a by-product of listings.

Where lawful, appropriate and permitted by the platform's privacy and data-governance rules, marketplace activity may progressively create structured data about:

```text
Properties
Listings and price history
Verification outcomes
Agent and developer reputation
Geography and location context
Search and demand signals
Saved properties
Enquiries and viewing activity
Fraud and anomaly signals
Property comparables
Verified transaction outcomes
Approved external and geospatial data
Infrastructure and neighbourhood context
Valuation and rental intelligence outputs
Ownership and title outcomes where lawfully available
```

This data may improve trust, risk, recommendation, valuation, property, pricing, investment, neighbourhood and market intelligence over time. Where lawful and reliable, verified transaction and ownership outcomes can strengthen the evidence base further.

The strategic flywheel is:

```text
Marketplace activity
        ↓
Higher-quality property, market, trust and demand data
        ↓
Better intelligence
        ↓
Better discovery, verification and evaluation
        ↓
More useful marketplace interactions
        ↓
More high-quality data
```

The data asset must not weaken privacy, consent, security, provenance or market-specific governance requirements.

### 2.2 Future Transaction Enablement

Dwelvara's initial marketplace journey ends primarily in enquiries and viewing requests. The long-term platform may extend beyond lead generation into transaction enablement.

Potential future capabilities include:

```text
Offer and negotiation workflows
Transaction milestones
Professional-service integrations
Payment or escrow integrations where legally and operationally appropriate
Financing or mortgage integrations
Verified transaction outcomes
```

These capabilities are not MVP requirements. When introduced, authoritative transaction state and consequential actions remain owned by Node.js/TypeScript workflows, with appropriate human, legal and operational controls.


---

# 3. Product Objectives

The MVP must validate five primary hypotheses.

## 3.1 Supply

Can legitimate property professionals be recruited to maintain genuine inventory?

Initial target:

```text
50–100 agents
1,000+ genuine listings
```

## 3.2 Demand

Will users:

* search properties;
* apply filters;
* inspect listings;
* review verification;
* save properties;
* make enquiries;
* request viewings;
* return to the marketplace?

## 3.3 Trust

Do users prefer listings carrying meaningful trust indicators such as:

```text
Verified Agent
Verified Location
Property Inspected
Documentation Reviewed
Verified Property
```

## 3.4 Agent Value

Can the platform provide sufficient value through:

* property exposure;
* qualified enquiries;
* lead management;
* professional reputation;
* verification;
* analytics;
* lightweight CRM;
* AI tools?

## 3.5 Commercial Value

Will property professionals eventually pay for:

* subscriptions;
* verification;
* qualified leads;
* premium placement;
* advanced analytics;
* AI tools;
* developer packages;
* market intelligence?

---

# 4. Architectural Principles

The platform must follow these principles from the first implementation.

### 4.1 Multi-Market by Design

Lagos is configuration and reference data, not application logic.

### 4.2 Property Is Not Listing

A physical property and an offer to sell or rent that property are different domain entities.

### 4.3 Verification Is a Domain

Verification must not be represented solely as a Boolean.

### 4.4 Asking Price Is Not Transaction Price

Marketplace asking-price data must remain distinguishable from verified completed transactions.

### 4.5 Node.js Owns Marketplace State

Node.js/TypeScript is authoritative for transactional marketplace workflows.

### 4.6 Python Owns Intelligence

Python owns AI, analytics, scoring, modelling and computational workloads.

### 4.7 Monorepo from Day One

The Node.js and Python applications must exist within the same repository from the first commit.

### 4.8 Independent Runtime Deployment

A monorepo does not mean a single deployment unit.

Node.js and Python must remain independently buildable and deployable.

### 4.9 Graceful Intelligence Failure

Core marketplace functionality must remain operational if Python intelligence services are unavailable.

### 4.10 Avoid Premature Microservices

Start with clearly separated applications and modules rather than dozens of independently deployed services.


### 4.11 Intelligence Should Benefit from the Data Flywheel

Marketplace events, property history, verification outcomes, demand signals, verified transaction outcomes and approved external data should be designed so they can support progressively better derived intelligence without weakening runtime ownership or privacy boundaries.

### 4.12 Transaction Enablement Remains Marketplace-Owned

Future offer, negotiation, payment, financing, closing or transaction-milestone capabilities are authoritative marketplace workflows. Node.js/TypeScript owns their state and permissions. Python may provide supporting risk, valuation, recommendation or explanatory intelligence but does not execute consequential transaction decisions independently.


---

### 4.7 Intelligence Is a Platform Layer

`apps/python-intelligence` is the Dwelvara Intelligence Platform, not merely a collection of isolated Python features.

The intelligence layer combines:

```text
Rules
Analytics
Machine-learning models
LLM capabilities
Intelligence services
AI agents
```

Machine-learning predictions remain first-class components of the intelligence layer. AI agents do not replace predictive models; they orchestrate approved intelligence services and tools for higher-level tasks.

```text
Data
  ↓
Rules / Analytics / ML Models / LLM Capabilities
  ↓
Signals and Predictions
  ↓
Intelligence Services
  ↓
AI Agents where appropriate
  ↓
Node.js marketplace workflows and user experiences
```

Node.js remains authoritative for marketplace state and consequential workflow decisions.

---

# 5. Backend Architecture

The platform deliberately uses two backend runtimes.

```text
                         USERS
                           │
                           ▼
                    NEXT.JS FRONTEND
                           │
                           ▼
              NODE.JS / TYPESCRIPT BACKEND
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
        ▼                  ▼                  ▼
   PostgreSQL        Object Storage        Redis
   Core Data             Media               │
        │                                    │
        │                                    ▼
        │                         PYTHON INTELLIGENCE
        │                             PLATFORM
        │                                │
        └────────────────────────────────┤
                                         │
              ┌──────────┬───────────────┼──────────────┐
              ▼          ▼               ▼              ▼
             AI        Trust           Fraud        Analytics
                                                         │
                                                         ▼
                                              Property Intelligence
                                                         │
                                                         ▼
                                                   Future ML/AVM
```

The permanent engineering rule is:

> **Node.js runs the marketplace. Python makes the marketplace intelligent.**

---

# 6. Node.js Responsibilities

Use Node.js/TypeScript when functionality primarily concerns:

* authenticated user interaction;
* CRUD;
* authoritative database state;
* permissions;
* business workflows;
* lifecycle changes;
* API endpoints;
* payments;
* subscriptions;
* notifications;
* transactional consistency.

Node.js owns:

```text
Authentication
RBAC
Users
Agent profiles
Developer profiles
Markets
Geography
Properties
Listings
Media workflows
Search APIs
Verification workflows
Enquiries
Viewing requests
Saved listings
Reports
Moderation
Subscriptions
Payments
Notifications
Administration
Audit logging
Marketplace event capture
```

---

# 7. Python Responsibilities

Use Python when functionality primarily concerns:

* AI;
* machine learning;
* analytics;
* statistics;
* computational scoring;
* recommendation algorithms;
* fraud detection;
* anomaly detection;
* advanced geospatial analysis;
* data engineering;
* large-scale aggregation;
* property valuation.

Python owns:

```text
AI listing assistant
AI property summaries
Trust scoring
Fraud/risk detection
Duplicate-property detection
Duplicate-image analysis
Recommendations
Property comparables
Property-market analytics
Advanced geospatial analytics
Statistical modelling
Data engineering
Future automated valuation models
```

Python must not become a second CRUD backend.

---

# 8. Hybrid Workflows

Where both runtimes are required:

```text
Node.js
   ↓
Authentication
Permissions
Orchestration
Authoritative data retrieval
   ↓
Python
   ↓
Computation
AI
Analytics
Intelligence
   ↓
Node.js
   ↓
Persistence
Workflow
User response
```

Example:

```text
Agent requests AI description
        ↓
Node.js authenticates agent
        ↓
Node.js retrieves property facts
        ↓
Python generates description
        ↓
Node.js returns draft
        ↓
Agent approves
        ↓
Node.js saves description
```

---

# 9. Monorepo Strategy

The project must use a monorepo from the first commit.

Do not build a standalone Next.js repository and later retrofit Python into it.

Required root:

```text
dwelvara/
│
├── apps/
│   ├── web/
│   └── python-intelligence/
│
├── packages/
│   ├── contracts/
│   ├── config/
│   ├── database/
│   └── testing/
│
├── infrastructure/
├── docs/
├── scripts/
├── .github/
│
├── AGENTS.md
├── CLAUDE.md
├── docker-compose.yml
├── package.json
├── pnpm-workspace.yaml
├── turbo.json
├── .env.example
├── .gitignore
└── README.md
```

---

# 10. Monorepo Tooling

Use:

```text
pnpm
pnpm workspaces
Turborepo
```

for TypeScript workspace and task orchestration.

Python remains part of the same repository but manages dependencies through:

```text
pyproject.toml
```

Do not force Python dependency management into npm/pnpm.

---

# 11. apps/web

`apps/web` contains:

```text
Next.js
React
TypeScript
Node.js transactional backend
Public marketplace
Agent dashboard
Developer dashboard
Admin dashboard
```

Recommended structure:

```text
apps/web/
│
├── src/
│   ├── app/
│   │   ├── (public)/
│   │   ├── (auth)/
│   │   ├── agent/
│   │   ├── developer/
│   │   ├── admin/
│   │   └── api/
│   │
│   ├── components/
│   │
│   ├── modules/
│   │   ├── identity/
│   │   ├── markets/
│   │   ├── geography/
│   │   ├── properties/
│   │   ├── listings/
│   │   ├── agents/
│   │   ├── developers/
│   │   ├── verification/
│   │   ├── search/
│   │   ├── enquiries/
│   │   ├── viewings/
│   │   ├── favourites/
│   │   ├── moderation/
│   │   ├── subscriptions/
│   │   ├── notifications/
│   │   └── administration/
│   │
│   ├── infrastructure/
│   │   ├── auth/
│   │   ├── database/
│   │   ├── storage/
│   │   ├── maps/
│   │   ├── payments/
│   │   ├── queue/
│   │   └── python-client/
│   │
│   ├── validators/
│   ├── types/
│   ├── lib/
│   └── utils/
│
├── tests/
├── public/
├── package.json
├── tsconfig.json
├── next.config.ts
└── README.md
```

---

# 12. apps/python-intelligence

The Python application lives at:

```text
apps/python-intelligence/
```

Recommended structure:

```text
apps/python-intelligence/
│
├── app/
│   ├── main.py
│   │
│   ├── api/
│   │   ├── health/
│   │   ├── ai/
│   │   ├── trust/
│   │   ├── fraud/
│   │   ├── duplicates/
│   │   ├── recommendations/
│   │   └── intelligence/
│   │
│   ├── domain/
│   │   ├── ai/
│   │   ├── trust/
│   │   ├── fraud/
│   │   ├── duplicates/
│   │   ├── recommendations/
│   │   ├── property_intelligence/
│   │   ├── analytics/
│   │   └── geospatial/
│   │
│   ├── services/
│   ├── schemas/
│   ├── repositories/
│   ├── workers/
│   ├── jobs/
│   ├── models/
│   │
│   ├── infrastructure/
│   │   ├── database/
│   │   ├── ai/
│   │   ├── queue/
│   │   ├── storage/
│   │   └── observability/
│   │
│   └── config/
│
├── tests/
│   ├── unit/
│   ├── integration/
│   └── contract/
│
├── pyproject.toml
├── Dockerfile
└── README.md
```

---

# 13. Shared Contracts

Cross-runtime contracts belong in:

```text
packages/contracts/
```

Recommended:

```text
packages/contracts/
│
├── schemas/
│   ├── ai/
│   ├── trust/
│   ├── risk/
│   ├── duplicates/
│   └── events/
│
├── openapi/
├── examples/
├── package.json
└── README.md
```

The language-neutral schema is the source of truth.

Use:

```text
JSON Schema
OpenAPI
```

Generate or mechanically derive runtime validators/models from the canonical contract wherever practical:

```text
Canonical JSON Schema/OpenAPI
        ↓
TypeScript → Zod
Python     → Pydantic
```

Do not independently hand-maintain equivalent TypeScript and Python schemas when generation/derivation is practical.

A TypeScript interface alone is not a shared Node.js/Python contract.

---

# 14. Contract Versioning

Every cross-runtime payload must be explicitly versioned.

Example:

```json
{
  "schemaVersion": "1.0",
  "propertyId": "uuid",
  "listingId": "uuid",
  "marketCode": "NG-LAGOS",
  "property": {
    "propertyType": "HOUSE",
    "bedrooms": 4,
    "bathrooms": 5,
    "floorArea": {
      "value": 450,
      "unit": "SQM"
    }
  }
}
```

Contract compatibility policy:

- additive, backward-compatible changes may remain within the same major version;
- consumers must tolerate optional additive fields;
- breaking changes require a new major contract version;
- every job and HTTP payload carries its contract/schema version;
- deprecated major versions must have an explicit retirement plan.

---

# 15. Shared Configuration

TypeScript configuration belongs in:

```text
packages/config/
```

Example:

```text
packages/config/
└── src/
    ├── markets/
    │   ├── ng-lagos.ts
    │   ├── ng-abuja.ts
    │   └── gb-london.ts
    ├── features/
    └── environment/
```

Python must not import TypeScript configuration files.

Python consumes required configuration through:

* persisted database configuration;
* environment variables;
* versioned contracts.

---

# 16. Database Package

Database infrastructure belongs in:

```text
packages/database/
```

Recommended:

```text
packages/database/
├── prisma/
│   ├── schema.prisma
│   ├── migrations/
│   └── seeds/
├── sql/
│   ├── extensions/
│   └── read-models/
├── src/
│   ├── client.ts
│   └── index.ts
├── package.json
└── README.md
```

The operational PostgreSQL database uses explicit schema ownership:

```text
PostgreSQL
├── marketplace
│   └── authoritative transactional state
└── intelligence
    └── derived intelligence state
```

Prisma owns migrations for the `marketplace` schema.

The Python intelligence application owns migrations for the `intelligence` schema using Alembic when intelligence persistence is introduced.

Phase 0 must prove both migration mechanisms and schema isolation without creating marketplace feature entities.

PostGIS must be enabled as a baseline PostgreSQL extension during Phase 0.

---

# 17. Database Ownership

## 17.1 Marketplace schema

Node.js owns the `marketplace` schema and all authoritative transactional tables, including:

```text
users
user_profiles
agent_profiles
developer_profiles

countries
administrative_areas
cities
districts
localities
markets

properties
listings
property_media
amenities
property_amenities

enquiries
viewing_requests
saved_listings

verifications
verification_checks
verification_evidence

reports
subscriptions
audit_events
```

Python must never independently redefine, migrate or directly mutate these tables.

## 17.2 Intelligence schema

Python owns the `intelligence` schema and derived tables such as:

```text
trust_scores
trust_score_components
risk_signals
duplicate_candidates
recommendations
property_statistics
market_statistics
analytics_aggregates
model_predictions
```

Alembic is the migration authority for Python-owned intelligence tables.

## 17.3 Python access to marketplace data

Python receives authoritative marketplace data through two approved mechanisms:

1. versioned service contracts for request-driven synchronous workflows; and
2. versioned, read-only PostgreSQL views for analytical/background workloads where direct database reads are justified.

Read-only views must expose only the fields required by intelligence workloads and must be treated as stable read contracts.

Python database credentials must have:

```text
marketplace schema → SELECT only on approved views
intelligence schema → required read/write permissions
```

Python must not receive general write privileges to the `marketplace` schema.

Node.js must not migrate Python-owned `intelligence` tables.

Cross-schema writes that bypass these ownership rules require an approved ADR.

---

# 18. Property Domain

A Property represents the persistent physical asset.

```text
Property
--------
id
public_id

property_type_id
property_subtype_id

address_id

bedrooms
bathrooms
toilets

floor_area_value
floor_area_unit

plot_area_value
plot_area_unit

parking_spaces

year_built
furnishing_status

created_at
updated_at
```

The following do not belong directly on Property:

```text
asking price
transaction type
listing status
listing agent
headline
publication date
expiry date
```

---

# 19. Listing Domain

A Listing represents a commercial offer associated with a Property.

```text
Listing
-------
id
public_id

property_id

listing_party_type
listing_party_id

transaction_type

asking_price_amount
asking_price_currency

price_period

headline
description

status

available_from
published_at
expires_at

is_featured

created_at
updated_at
```

Initial transaction types:

```text
SALE
RENT
```

Future:

```text
SHORT_LET
LEASE
AUCTION
```

---

# 20. Property and Listing Relationship

Example:

```text
PROPERTY
4-bedroom detached house
Lekki Phase 1
        │
        ├── Listing 2026
        │   SALE
        │   NGN 280m
        │
        ├── Listing 2028
        │   RENT
        │   NGN 18m/year
        │
        └── Listing 2031
            SALE
            NGN 410m
```

This enables:

* listing history;
* price history;
* time-on-market analysis;
* duplicate detection;
* transaction history;
* comparables;
* future valuation.

---

# 21. Listing Lifecycle

Statuses:

```text
DRAFT
PENDING_REVIEW
ACTIVE
UNDER_OFFER
SOLD
LET
WITHDRAWN
EXPIRED
SUSPENDED
REJECTED
```

Lifecycle:

```text
DRAFT
  ↓
PENDING_REVIEW
  ↓
ACTIVE
  ├── UNDER_OFFER
  ├── SOLD
  ├── LET
  ├── WITHDRAWN
  ├── EXPIRED
  └── SUSPENDED
```

Node.js enforces legal transitions.

Python must not change listing lifecycle state.

---

# 22. Market Domain

A Market represents a commercial deployment.

Examples:

```text
NG-LAGOS
NG-ABUJA
GB-LONDON
```

```text
Market
------
id
code

country_id
city_id

name

default_currency
default_locale
default_timezone
default_area_unit

launch_status
is_active

created_at
updated_at
```

---

# 23. Geographic Architecture

Do not hard-code Nigerian geography.

Use:

```text
Country
   ↓
Administrative Area
   ↓
City
   ↓
District
   ↓
Locality
   ↓
Development
```

Levels may be optional depending on the country.

Lagos:

```text
Nigeria
└── Lagos State
    └── Lagos
        └── Eti-Osa
            └── Lekki Phase 1
```

Abuja:

```text
Nigeria
└── Federal Capital Territory
    └── Abuja
        └── Abuja Municipal Area Council
            └── Maitama
```

London:

```text
United Kingdom
└── England
    └── London
        └── Westminster
            └── Mayfair
```

---

# 24. Currency

Never store formatted currency strings.

Wrong:

```text
₦280,000,000
```

Correct:

```text
amount = 280000000
currency = NGN
```

UK:

```text
amount = 850000
currency = GBP
```

Presentation formatting occurs separately.

---

# 25. Measurements

Support:

```text
SQM
SQFT
ACRE
HECTARE
```

Store:

```text
value
unit
```

Never assume metric units globally.

---

# 26. Address Architecture

```text
Address
-------
id

address_line_1
address_line_2

locality_id
district_id
city_id
administrative_area_id
country_id

postal_code

latitude
longitude

created_at
updated_at
```

Postal code must not be universally mandatory.

---

# 27. Location Privacy

Support:

```text
EXACT
APPROXIMATE
LOCALITY_ONLY
```

Internal systems may retain precise location while the public marketplace displays approximate location.

---

# 28. User Roles

Initial:

```text
SEEKER
AGENT
DEVELOPER
VERIFICATION_OFFICER
MODERATOR
ADMIN
SUPER_ADMIN
```

Future:

```text
OWNER
INSTITUTIONAL_SELLER
```

Authentication and RBAC belong to Node.js.

---

# 29. Property Seeker

Anonymous users can:

* search;
* filter;
* view listings;
* inspect verification;
* view public professional profiles.

Registered users can additionally:

* save listings;
* make enquiries;
* request viewings;
* report listings;
* manage enquiry history.

---

# 30. Agent

Agents can:

* register;
* create profiles;
* submit verification;
* create properties;
* create listings;
* upload media;
* manage inventory;
* receive enquiries;
* manage leads;
* respond to viewing requests;
* request property verification;
* access analytics;
* use AI listing assistance.

---

# 31. Developer

Hierarchy:

```text
Developer Organisation
        ↓
Development
        ↓
Property / Unit
        ↓
Listing
```

Developers can manage:

* developments;
* units;
* availability;
* pricing;
* payment plans;
* completion dates;
* media;
* enquiries.

---

# 32. Private Owners

Private-owner listings are not part of the initial launch.

Future workflow:

```text
Owner registration
      ↓
Identity verification
      ↓
Ownership evidence
      ↓
Manual review
      ↓
Owner approval
      ↓
Listing permission
```

---

# 33. Verification Domain

Do not implement verification as:

```text
is_verified = true
```

Use:

```text
Verification
VerificationCheck
VerificationEvidence
VerificationEvent
```

Targets:

```text
USER
AGENT
DEVELOPER
PROPERTY
LISTING
TRANSACTION
```

---

# 34. Verification Ownership

Node.js owns:

```text
Verification requests
Evidence upload
Reviewer assignment
Checks
Approval
Rejection
Expiry
Permissions
Audit trail
```

Python may provide:

```text
Risk signals
Duplicate signals
Anomaly detection
Document-analysis assistance
Trust calculations
```

Python advises.

Node.js owns the verification decision.

---

# 35. Verification Status

```text
NOT_STARTED
PENDING
UNDER_REVIEW
VERIFIED
FAILED
EXPIRED
CANCELLED
```

---

# 36. Verification Display

Avoid:

```text
✓ Verified
```

Prefer:

```text
AGENT

✓ Identity verified
✓ Business verified
✓ Contact details verified
```

and:

```text
PROPERTY

✓ Location verified
✓ Property inspected
◐ Documentation under review
```

This prevents a broad badge from implying checks that were never performed.

---

# 37. Trust Score

Trust scoring belongs to Python.

Initial implementation must be:

```text
Rules-based
Explainable
Versioned
Auditable
```

Potential inputs:

```text
Identity verification
Business verification
Response rate
Property verification rate
Complaint rate
Listing accuracy
Account age
Verified transactions
Ratings
```

Example:

```json
{
  "score": 92,
  "model": "agent-trust-rules",
  "version": "1.0",
  "components": [
    {
      "name": "identity_verification",
      "contribution": 20
    },
    {
      "name": "business_verification",
      "contribution": 20
    }
  ]
}
```

---

# 38. Media

Node.js owns:

* signed uploads;
* metadata;
* image ordering;
* primary image;
* deletion;
* permissions.

Object storage contains binaries.

Python may asynchronously perform:

* duplicate-image detection;
* image similarity;
* image-quality analysis;
* suspicious-image analysis.

---

# 39. Amenities

Use:

```text
Amenity
PropertyAmenity
```

rather than hundreds of Boolean columns.

This allows market-specific amenities without database redesign.

---

# 40. Search

Node.js owns primary search.

Filters:

```text
Market
City
Locality
Transaction type
Property type
Minimum price
Maximum price
Bedrooms
Bathrooms
Amenities
Verified agent
Verified property
```

PostgreSQL is sufficient for the initial marketplace.

---

# 41. Search Ranking

Future hybrid model:

```text
Node.js
   ↓
Eligible Listings
   ↓
Python Ranking
   ↓
Ranked IDs
   ↓
Node.js
   ↓
User
```

Python ranking is not required for MVP.

---

# 42. Property Detail Page

Required:

```text
Media Gallery
Price
Headline
Location
Property Facts
Description
Amenities
Verification
Map
Agent / Developer
AI Summary
Enquire
Request Viewing
Save
Report
```

---

# 43. Enquiries

Node.js owns enquiry lifecycle.

Statuses:

```text
NEW
CONTACTED
VIEWING_REQUESTED
VIEWING_SCHEDULED
NEGOTIATING
CONVERTED
CLOSED
SPAM
```

---

# 44. Viewing Requests

Node.js owns:

```text
Request
Accept
Decline
Reschedule
Confirm
Complete
Cancel
No-show
```

Types:

```text
IN_PERSON
VIDEO
VIRTUAL
```

---

# 45. Saved Listings

```text
SavedListing
------------
user_id
listing_id
created_at
```

Node.js owns this domain.

---

# 46. Reports and Moderation

Categories:

```text
FAKE_PROPERTY
INCORRECT_INFORMATION
PROPERTY_UNAVAILABLE
SUSPICIOUS_AGENT
DUPLICATE_LISTING
MISLEADING_PRICE
INAPPROPRIATE_CONTENT
OTHER
```

Node.js owns moderation.

Python supplies supporting risk intelligence.

---

# 47. Fraud and Risk

Python may analyse:

```text
Suspicious pricing
Unusual listing volume
Duplicate properties
Duplicate images
Repeated complaints
Abnormal account behaviour
Geographic anomalies
Repeated relisting
Suspicious verification patterns
```

Python produces:

```text
RiskSignal
```

Node.js/admin decides whether action is required.

Python must not automatically suspend marketplace entities.

---

# 48. Duplicate Property Detection

Python may analyse:

```text
Coordinates
Address similarity
Property type
Bedrooms
Bathrooms
Floor area
Plot area
Images
Descriptions
Agent data
```

Output:

```text
Candidate:
P-123

Possible Match:
P-048

Similarity:
92%
```

Do not automatically merge records.

---

# 49. AI Listing Assistant

Python owns AI generation.

```text
Agent
   ↓
Structured property facts
   ↓
Node.js validation
   ↓
Python AI
   ↓
Generated description
   ↓
Node.js
   ↓
Agent review
   ↓
Save approved content
```

AI must not invent property facts.

---

# 50. AI Property Summary

Python generates summaries exclusively from authoritative structured information.

The output must accurately distinguish verified and unverified facts.

---

# 51. Recommendation Engine

Python owns recommendations.

Initial inputs:

```text
Location
Price band
Property type
Bedrooms
Transaction type
```

Future:

```text
Search history
Views
Saved listings
Enquiries
Viewing activity
Behavioural similarity
```

Do not introduce complex ML until sufficient behavioural data exists.

---

# 52. Asking Price vs Transaction Price

Mandatory:

```text
ASKING PRICE ≠ TRANSACTION PRICE
```

Future:

```text
PropertyTransaction
-------------------
id
property_id
listing_id

transaction_type

transaction_price_amount
transaction_price_currency

transaction_date

verification_status
```

Analytics must preserve the distinction.

---

# 53. Property Comparables

Python owns comparable analysis.

Inputs may include:

```text
Location
Property type
Bedrooms
Bathrooms
Floor area
Plot area
Amenities
Age
Verification
Listing date
```

Outputs:

```text
Comparable properties
Similarity scores
Price distribution
Median
Confidence
```

---

# 54. Market Analytics

Python owns significant analytical workloads:

```text
Median asking price
Average asking price
Price per sqm
Price per sqft
Price distributions
Supply by locality
Demand by locality
Rental trends
Sale trends
Time on market
Listing conversion
```

---

# 55. Geospatial Intelligence

Basic filtering:

```text
Node.js + PostgreSQL/PostGIS
```

Advanced analytics:

```text
Python
GeoPandas
Shapely
PostGIS
```

Potential capabilities:

```text
Radius analysis
Distances
Spatial clustering
Point-in-polygon
Amenity proximity
Transport proximity
Heatmaps
Spatial comparables
Demand analysis
```

---

# 56. Automated Valuation Model

Future AVM development belongs to Python.

Possible progression:

```text
Comparable statistics
      ↓
Regression
      ↓
Gradient boosting
      ↓
Spatial modelling
      ↓
Ensemble models
```

Do not build an AVM during MVP.

---

# 57. Marketplace Events

Node.js records:

```text
search_performed
listing_viewed
filter_applied
property_saved
enquiry_created
viewing_requested
agent_profile_viewed
verification_viewed
listing_reported
listing_created
listing_published
```

Python may consume events for:

```text
Analytics
Recommendations
Demand modelling
Fraud detection
Market intelligence
```

---

# 58. Data Engineering

Significant data engineering belongs to Python.

Future:

```text
Operational PostgreSQL
        ↓
Python Extraction
        ↓
Raw / Bronze
        ↓
Transformation
        ↓
Silver
        ↓
Analytics / Gold
```

Potential future tools:

```text
Python
Airflow
dbt
Object storage
Analytics warehouse
```

Introduce Spark only if actual data scale justifies it.

---

# 59. Synchronous Node.js/Python Communication

Use synchronous HTTP only when the user needs the result immediately.

Examples:

```text
AI listing description
AI property summary
Immediate duplicate check
Certain intelligence lookups
```

FastAPI endpoints may include:

```text
GET  /health

POST /v1/ai/listing-description
POST /v1/ai/property-summary

POST /v1/trust/score

POST /v1/duplicates/property

POST /v1/risk/listing
```

---

# 60. Node.js Python Client

Centralise Python communication under:

```text
apps/web/src/infrastructure/python-client/
```

Example:

```text
python-client/
├── client.ts
├── ai.ts
├── trust.ts
├── risk.ts
├── duplicates.ts
└── errors.ts
```

Do not scatter raw FastAPI URLs throughout business modules.

---

# 61. Asynchronous Processing

Use background jobs for:

```text
Trust recalculation
Fraud scans
Image processing
Duplicate detection
Recommendations
Analytics aggregation
Market statistics
```

Redis is the initial queue transport, but Redis itself does not define cross-language interoperability.

Node.js and Python must exchange jobs using a versioned, language-neutral JSON job envelope rather than assuming BullMQ, Celery or another framework's private protocol is cross-compatible.

Canonical envelope:

```json
{
  "schemaVersion": "1.0",
  "jobId": "uuid",
  "jobType": "trust.recalculate",
  "occurredAt": "ISO-8601",
  "correlationId": "uuid",
  "attempt": 1,
  "payload": {}
}
```

Required characteristics:

```text
Versioned payload
Idempotency
Correlation IDs
Retry policy
Dead-letter/failure handling
Observable job status
Explicit ownership of result persistence
```

Initial architecture:

```text
Node.js
   ↓
Versioned job producer
   ↓
Redis
   ↓
Python worker
   ↓
Derived result
   ↓
intelligence schema
```

Do not assume BullMQ jobs can be consumed directly by a Python worker.

Select the smallest queue implementation that honours the shared envelope and can be tested from both runtimes.

Do not introduce Kafka during MVP without a demonstrated requirement.

---

# 62. Failure Isolation

If Python is unavailable:

```text
Authentication        ✓
Property creation     ✓
Listing creation      ✓
Search                ✓
Enquiries             ✓
Viewing requests      ✓
Moderation            ✓
Admin operations      ✓
```

Python-dependent functionality may degrade:

```text
AI description        unavailable
Recommendations       fallback/hidden
Trust recalculation   delayed
Fraud processing      queued
```

The marketplace must remain usable.

---

# 63. Agent Dashboard

Operational metrics:

```text
Active listings
Listing views
Enquiries
Viewing requests
Verified properties
Response rate
Trust score
```

Node.js serves operational information.

Python may calculate:

```text
Trust score
Performance trends
Market benchmarks
```

---

# 64. Lightweight CRM

Lead lifecycle:

```text
NEW
 ↓
CONTACTED
 ↓
VIEWING
 ↓
NEGOTIATING
 ↓
WON / LOST
```

Do not build an enterprise CRM during MVP.

---

# 65. Administration

Node.js owns:

```text
User management
Agent approval
Developer approval
Listing moderation
Verification queues
Reports
Reference data
Market configuration
Audit logs
```

Python may surface:

```text
High-risk listings
Possible duplicates
Price anomalies
Suspicious accounts
```

---

# 66. Audit Logging

Node.js owns immutable transactional audit events.

Examples:

```text
AGENT_APPROVED
AGENT_SUSPENDED
LISTING_APPROVED
LISTING_SUSPENDED
VERIFICATION_COMPLETED
VERIFICATION_FAILED
PRICE_CHANGED
```

Suggested:

```text
AuditEvent
----------
id

actor_user_id
action

entity_type
entity_id

previous_state
new_state

metadata

created_at
```

---

# 67. Authentication

Recommended:

```text
Supabase Auth
```

Node.js owns:

```text
Sign up
Sign in
Sign out
Email verification
Password reset
Session refresh
```

Python must not manage passwords.

---

# 68. Authorisation

RBAC belongs to Node.js.

Never rely exclusively on frontend controls.

Python internal APIs must also be protected from unauthorised direct invocation.

For the initial architecture, Node.js authenticates to protected Python endpoints with a dedicated internal service credential supplied through environment/secrets management. `/health` remains unauthenticated for local/container health checks. A stronger workload-identity mechanism may replace this later without changing domain contracts.

---

# 69. Payments and Subscriptions

Node.js owns:

```text
Plans
Subscriptions
Entitlements
Payment initiation
Payment verification
Payment webhooks
Refund workflows
Billing state
```

Potential plans:

```text
FREE
PROFESSIONAL
PREMIUM
ENTERPRISE
```

Potential entitlements:

```text
Listing limits
Verification
Analytics
CRM functionality
Priority placement
AI tools
Qualified leads
Developer features
Market intelligence
```

---

# 70. Notifications

Node.js orchestrates:

```text
Email
SMS
Push
WhatsApp
```

Potential notifications:

```text
New enquiry
Viewing request
Viewing confirmation
Verification decision
Listing approval
Subscription renewal
```

---

# 71. Internationalisation

Use:

```text
next-intl
```

or equivalent.

Initial:

```text
en-NG
```

Future:

```text
en-GB
```

Do not embed market-specific strings throughout domain logic.

---

# 72. Phone Numbers

Store phone numbers using E.164:

```text
+234...
+44...
```

Validation rules may be market-aware.

---

# 73. Technical Stack

## Web

```text
Next.js
React
TypeScript
Tailwind CSS
shadcn/ui
```

## Node.js

```text
Node.js
TypeScript
Next.js server/API layer
Prisma
Zod
Supabase Auth
```

A dedicated NestJS application may be introduced later only if complexity justifies it.

## Python

```text
Python 3.12+
FastAPI
Pydantic
pytest
```

Introduce when needed:

```text
SQLAlchemy
Pandas
Polars
scikit-learn
GeoPandas
Shapely
```

## Database

```text
PostgreSQL
PostGIS when required
```

## Queue

```text
Redis
```

## Storage

```text
Supabase Storage
```

or another managed object-storage provider.

## Maps

```text
Mapbox
```

behind a provider abstraction where practical.

---

# 74. Local Development

Root:

```text
docker-compose.yml
```

Initial services:

```text
web
python-intelligence
postgres
redis
```

Suggested local ports:

```text
Next.js              3000
FastAPI              8000
PostgreSQL           5432
Redis                6379
```

All ports must remain configurable.

---

# 75. Health Checks

Node.js:

```text
GET /api/health
```

Python:

```text
GET /health
```

Docker health checks should verify both services.

---

# 76. Environment Configuration

Root:

```text
.env.example
```

Example:

```text
NODE_ENV=development

NEXT_PUBLIC_APP_URL=http://localhost:3000

DATABASE_URL=

SUPABASE_URL=
SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=

DEFAULT_MARKET=NG-LAGOS

MAP_PROVIDER=mapbox
MAP_API_KEY=

PYTHON_INTELLIGENCE_URL=http://localhost:8000

AI_PROVIDER=anthropic
AI_API_KEY=

REDIS_URL=redis://localhost:6379
```

Never commit real secrets.

---

# 77. Security

Implement:

* server-side authorisation;
* input validation;
* rate limiting;
* secure authentication;
* safe sessions;
* private verification evidence;
* signed storage access;
* file-type validation;
* upload-size limits;
* secure secrets;
* audit logging;
* least-privilege database access;
* protected internal Python APIs;
* AI input/output safeguards.

---

# 78. Privacy

Separate:

```text
PUBLIC MARKETPLACE DATA

PRIVATE USER DATA

SENSITIVE VERIFICATION DATA
```

These require different access policies.

The architecture must be capable of supporting stronger market-specific retention/privacy requirements, including future UK deployment.

---

# 79. Lagos Market Configuration

Example:

```typescript
export const lagosMarketConfig = {
  marketCode: "NG-LAGOS",
  countryCode: "NG",
  cityCode: "LAGOS",

  locale: "en-NG",
  currency: "NGN",
  timezone: "Africa/Lagos",

  phoneCountryCode: "+234",

  defaultAreaUnit: "SQM",
  measurementSystem: "metric",

  enabledTransactionTypes: [
    "SALE",
    "RENT"
  ],

  enabledPropertyTypes: [
    "HOUSE",
    "APARTMENT",
    "LAND"
  ]
};
```

---

# 80. Lagos Seed Geography

Seed initial reference data such as:

```text
Nigeria
└── Lagos State
    └── Lagos
        ├── Lekki
        │   └── Lekki Phase 1
        ├── Ikoyi
        ├── Victoria Island
        ├── Ikeja
        │   └── Ikeja GRA
        ├── Ajah
        ├── Yaba
        ├── Surulere
        ├── Maryland
        ├── Magodo
        └── Banana Island
```

Treat this as reference data rather than hard-coded domain behaviour.

---

# 81. Abuja Expansion

Adding Abuja should primarily require:

```text
Create NG-ABUJA market

Load FCT geography

Load Abuja city

Load districts / Area Councils

Load localities

Configure market

Enable market
```

No new Property, Listing, Verification or Agent subsystem should be required.

---

# 82. UK Architectural Test

Configure test-only:

```text
GB-LONDON
```

Example characteristics:

```text
currency = GBP
locale = en-GB
timezone = Europe/London
phoneCountryCode = +44
defaultAreaUnit = SQFT
```

The UK configuration exists initially to validate architecture.

It is not part of the Lagos MVP.

---

# 83. Development Seed Data

Generate fictional development data:

```text
50 agents
5 developers
250 properties
300 listings

verification records
enquiries
viewing requests
saved listings
reports
trust scores
duplicate candidates
```

Do not use real personal identities unnecessarily.

---

# 84. Testing Strategy

Implement:

```text
Unit tests
Integration tests
API tests
Component tests
End-to-end tests
Contract tests
```

Contract testing is mandatory because the platform has TypeScript and Python runtimes.

---

# 85. Contract Tests

Canonical payload examples from:

```text
packages/contracts/examples/
```

must validate successfully in:

```text
TypeScript → Zod
Python     → Pydantic
```

CI should fail if the two implementations diverge.

---

# 86. Seeker End-to-End Test

```text
Visit marketplace
      ↓
Select Lagos
      ↓
Search Lekki
      ↓
Choose Buy
      ↓
Filter 3+ bedrooms
      ↓
Filter verified property
      ↓
Open listing
      ↓
Review verification
      ↓
Register
      ↓
Save listing
      ↓
Create enquiry
      ↓
Request viewing
```

---

# 87. Agent End-to-End Test

```text
Register
      ↓
Create professional profile
      ↓
Submit verification
      ↓
Admin approves
      ↓
Create Property
      ↓
Create Listing
      ↓
Upload images
      ↓
Request AI description
      ↓
Python generates draft
      ↓
Agent approves
      ↓
Submit listing
      ↓
Admin approves
      ↓
Listing published
      ↓
Receive enquiry
      ↓
Manage lead
```

---

# 88. Python Failure Test

Automated tests must demonstrate:

```text
Python unavailable
       ↓
Core marketplace remains operational
```

Search, listings, enquiries, viewings and moderation must continue to work.

---

# 89. Multi-Market Test

Tests must support simultaneous:

```text
NG-LAGOS
NG-ABUJA
GB-LONDON
```

Validate:

```text
Lagos listing appears only in relevant results.

Abuja listing appears only in relevant results.

Lagos defaults to NGN/SQM.

London defaults to GBP/SQFT.

Property and Listing models remain unchanged.
```

---

# 90. CI/CD

Start with one workflow:

```text
.github/workflows/ci.yml
```

It should contain parallel jobs for:

```text
Web lint/typecheck/test/build
Python lint/typecheck/test
Contract tests
Migration/schema checks
Container build checks
Security/dependency checks where practical
```

Recommended Python tooling:

```text
ruff
mypy
pytest
```

Split CI into additional workflow files only when scale, permissions or runtime duration justify it.

---

# 91. Independent Deployment

Although the code lives in one repository:

```text
apps/web
```

and:

```text
apps/python-intelligence
```

must remain independently deployable.

Conceptually:

```text
Git Repository
      │
      ├── apps/web
      │      ↓
      │   Web Deployment
      │
      └── apps/python-intelligence
             ├── FastAPI Deployment
             └── Worker Deployment
```

A Python-only change should not automatically require rebuilding unrelated web infrastructure unless the deployment platform requires it.

---

# 92. Documentation

Use:

```text
docs/
│
├── product/
│   └── product-specification.md
│
├── architecture/
│   ├── overview.md
│   ├── system-context.md
│   ├── node-python-boundary.md
│   ├── database.md
│   ├── geography.md
│   ├── verification.md
│   │
│   └── decisions/
│
├── development/
│   ├── ai-agent-workflow.md
│   ├── development-workflow.md
│   ├── coding-standards.md
│   └── testing-strategy.md
│
├── plans/
│   └── phase-0-implementation-plan.md
│
└── api/
    ├── node-api.md
    └── python-intelligence-api.md
```

Repository-level agent instructions are separated from product documentation:

```text
AGENTS.md
→ shared repository rules for all coding agents, including Codex

CLAUDE.md
→ Claude Code-specific operating instructions
```

The product specification and accepted ADRs remain authoritative. Tool-specific instruction files must not redefine product architecture.

---

# 93. Architecture Decision Records

Create ADRs for significant decisions that are actually made. Do not create speculative ADRs merely to satisfy a fixed count.

Initial ADRs should cover the decisions required to establish the foundation, including:

```text
ADR-001-monorepo
ADR-002-node-python-boundary
ADR-003-property-vs-listing
ADR-004-market-and-geography
ADR-005-database-schema-ownership
ADR-006-cross-runtime-contracts
ADR-007-sync-vs-async-communication
```

Additional ADRs should be added when a real architectural choice is approved, for example object storage provider, authentication provider or deployment platform.

Each ADR must contain:

```text
Context
Decision
Alternatives considered
Consequences
```

Do not silently overturn an accepted ADR.

---

# 94. Node.js vs Python Responsibility Matrix

| Capability            | Node.js / TypeScript | Python               |
| --------------------- | -------------------- | -------------------- |
| Authentication        | Primary              | No                   |
| RBAC                  | Primary              | No                   |
| Users                 | Primary              | No                   |
| Agent profiles        | Primary              | No                   |
| Developer profiles    | Primary              | No                   |
| Markets               | Primary              | Consume              |
| Geography CRUD        | Primary              | Consume              |
| Property CRUD         | Primary              | Read                 |
| Listing CRUD          | Primary              | Read                 |
| Listing lifecycle     | Primary              | No                   |
| Media upload          | Primary              | Analyse              |
| Search filtering      | Primary              | Optional ranking     |
| Enquiries             | Primary              | Analyse              |
| Viewing requests      | Primary              | Analyse              |
| Saved listings        | Primary              | Recommendation input |
| Verification workflow | Primary              | Supporting signals   |
| Moderation            | Primary              | Risk signals         |
| Audit logging         | Primary              | No                   |
| Payments              | Primary              | No                   |
| Subscriptions         | Primary              | No                   |
| Notifications         | Primary              | No                   |
| AI listing assistant  | Orchestrate          | Primary              |
| AI property summary   | Orchestrate          | Primary              |
| Trust scoring         | Consume              | Primary              |
| Fraud detection       | Act on signal        | Primary              |
| Duplicate detection   | Review workflow      | Primary              |
| Recommendations       | Serve                | Primary              |
| Property comparables  | Consume              | Primary              |
| Market analytics      | Consume/basic        | Primary              |
| Rental yield          | Consume              | Primary              |
| AVM                   | Consume              | Primary              |
| Basic geography       | Primary              | Consume              |
| Advanced geospatial   | Consume              | Primary              |
| Data pipelines        | No                   | Primary              |
| Machine learning      | No                   | Primary              |
| Statistical modelling | No                   | Primary              |

---

# 95. MVP Scope

Build:

```text
Monorepo foundation

Authentication
RBAC

Lagos market
Geography

Users
Agents
Developers

Properties
Listings
Amenities
Media

Search
Filters

Verification

Enquiries
Viewing requests
Saved listings

Reports
Moderation

Agent dashboard
Lightweight CRM
Admin dashboard

Audit logs
Marketplace events

Python FastAPI
Python workers

AI listing assistant
AI property summary
Trust score
Basic duplicate detection
```

---

# 96. Non-MVP

Do not build yet:

```text
Mortgage marketplace

Escrow

Conveyancing

Full online property transaction

Nationwide launch

UK public launch

Advanced recommendation ML

Advanced fraud ML

Automated valuation model

Blockchain

Cryptocurrency

Native mobile apps

Large-scale Spark platform

Enterprise CRM
```

---

# 97. Implementation Roadmap

## Phase 0 — Architecture and Monorepo Foundation

Create:

```text
apps/web
apps/python-intelligence

packages/contracts
packages/config
packages/database
packages/testing

docs
infrastructure
scripts
.github/workflows

pnpm-workspace.yaml
turbo.json
docker-compose.yml
.env.example
README.md
AGENTS.md
CLAUDE.md
```

Configure:

```text
Next.js
TypeScript
Python 3.12+
FastAPI
PostgreSQL + PostGIS
Redis
Prisma
Alembic foundation
Docker Compose
pnpm
Turborepo
```

Establish:

```text
marketplace PostgreSQL schema → Prisma/Node.js ownership
intelligence PostgreSQL schema → Alembic/Python ownership
Python read-only access boundary to approved marketplace views
versioned JSON Schema/OpenAPI contracts
derived Zod/Pydantic validation
protected Node.js → Python internal HTTP
versioned Redis job envelope
single CI workflow with parallel jobs
```

Create working health checks.

Create one shared cross-runtime contract and canonical example.

Prove Zod and Pydantic validate the same contract.

Prove Node.js can call FastAPI through the central Python client.

Prove one asynchronous versioned job can be produced and consumed across the language boundary.

Prove migration/schema ownership without creating marketplace feature entities.

Do not implement Property, Listing, Agent, Developer, Verification, Search, Enquiry, Viewing or other marketplace feature functionality during Phase 0.

### Phase 0 definition of done

Phase 0 is complete only when:

```text
Monorepo works.
apps/web runs.
apps/python-intelligence runs.
PostgreSQL + PostGIS starts and is reachable.
Redis starts and is reachable.
marketplace and intelligence schemas are isolated.
Prisma migration mechanism works for marketplace ownership.
Alembic migration mechanism works for intelligence ownership.
Node.js health check passes.
Python health check passes.
Node.js can call protected Python endpoints.
Shared contracts work.
Zod validates the canonical contract.
Pydantic validates the same canonical contract.
A versioned Redis job crosses Node.js → Python successfully.
Python has no write access to authoritative marketplace state.
Node.js tests run.
Python tests run.
Contract tests run.
Docker Compose works.
CI passes.
Both runtimes build independently.
Both runtimes have independently deployable artifacts.
Python failure does not prevent the Node.js application from starting or serving its health/core foundation.
```

Only then should Phase 1 begin.

---

# 98. Phase 1 — Platform Foundation

Implement:

```text
Authentication
RBAC

Market domain
Country
Geography

Lagos configuration

Database migrations
Seed infrastructure

Storage abstraction
Audit foundation
```

---

# 99. Phase 2 — Marketplace Core

Node.js:

```text
Property
Listing
Amenities
Media
Listing lifecycle
Search
Filters
Property detail
```

Python must not be required for these workflows.

---

# 100. Phase 3 — Supply

Implement:

```text
Agent registration
Agent profile

Developer organisation
Developer profile

Agent dashboard

Property creation
Listing creation
Listing management
```

---

# 101. Phase 4 — Trust

Node.js:

```text
Verification requests
Evidence
Admin review
Verification status
Audit events
```

Python:

```text
Trust score
Basic duplicate-property detection
```

---

# 102. Phase 5 — Engagement

Node.js:

```text
Saved listings
Enquiries
Viewing requests
CRM
Reports
Moderation
```

---

# 103. Phase 6 — AI

Python:

```text
AI listing assistant
AI property summary
```

Node.js:

```text
Authentication
Permissions
Data retrieval
Python orchestration
Persistence
User response
```

---

# 104. Phase 7 — Analytics and Risk

Python:

```text
Basic fraud signals
Price anomaly rules
Agent analytics
Market analytics
```

Node.js:

```text
Risk review interface
Human decisions
Moderation actions
```

---

# 104A. Intelligence Layer Architecture

The Python application is the first-class Dwelvara Intelligence Platform. Its purpose is to convert marketplace and approved external data into trustworthy signals, predictions, recommendations, explanations and agent-assisted experiences.

```text
Marketplace + Approved External Data
          ↓
Rules / Analytics / ML Models / LLM Capabilities
          ↓
Signals + Predictions
          ↓
Intelligence Services
          ↓
AI Agents / Product Integrations
          ↓
Node.js Marketplace Workflows
```

Core intelligence domains are:

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

Machine-learning models remain explicit components of this platform. They produce predictions, probabilities, scores or rankings such as fraud probability, duplicate probability, recommendation ranking, price anomaly scores and future automated valuations.

Intelligence services combine those model outputs with deterministic rules, statistics, marketplace facts and other approved signals. A model prediction is not itself a complete marketplace decision.

AI agents are optional orchestration components above approved tools and intelligence services. Potential future agents include:

```text
Property assistant
Buyer assistant
Agent copilot
Verification assistant
Market intelligence agent
```

Agents may call approved tools and intelligence services, but they must not replace authoritative Node.js workflows, invent property facts, silently change verification state, directly mutate marketplace tables, or treat LLM output as verified fact.

Consequential actions remain controlled by Node.js and, where required, human review.

User-facing intelligence should distinguish marketplace facts, derived statistics, rule-based signals, ML predictions, LLM-generated explanations and human verification decisions. Where practical, outputs should carry provenance, model/rule version, timestamp and appropriate confidence or uncertainty information.

---

## 104B. Evidence-Backed Property Evaluation

Dwelvara should progressively assemble an evidence-backed property evaluation from independently governed intelligence services rather than relying on a single opaque score. Depending on data availability and market, a property evaluation may include:

```text
Property verification status
Professional verification status
Title/document review status
Estimated market value and uncertainty interval
Asking-price assessment
Comparable properties
Area price trend
Estimated rent
Estimated gross rental yield
Flood/environmental exposure
Road and transport accessibility
Infrastructure context
Fraud/risk indicators
Overall confidence / evidence completeness
```

Not every field will be available in every market. Missing or low-quality evidence must be represented as unavailable or uncertain rather than inferred as fact.

An AI property intelligence agent may answer questions such as "Is this property a good investment?" only by orchestrating approved intelligence services and grounding the response in available evidence. It should expose the important evidence, assumptions, provenance and uncertainty behind the answer rather than present an unsupported recommendation.

### Neighbourhood Intelligence

Neighbourhood Intelligence is a first-class intelligence domain. Subject to lawful, reliable and market-appropriate data sources, it may combine:

```text
Flood and environmental exposure
Road accessibility
Transport connectivity
Infrastructure context
Power / utility context where reliable data exists
Schools and education access
Healthcare access
Amenities
Area development signals
Local supply and demand
Price and rental trends
```

Measures must distinguish objective source data, derived statistics and subjective assessments. Provenance and freshness should be retained where practical.

### Pricing and Investment Intelligence

Pricing Intelligence may combine asking-price history, verified transaction data where available, comparables, market statistics, property characteristics, geospatial context and future AVM outputs.

Investment Intelligence may combine pricing intelligence with rental estimates, estimated yield, market trends, demand, neighbourhood context and trust/risk signals. It is decision support, not financial advice, and should communicate assumptions and uncertainty.

---

# 105. Future Intelligence

Python:

```text
Recommendations
Property comparables
Pricing intelligence
Neighbourhood intelligence
Investment intelligence
Advanced geospatial analytics
Rental estimates and yield
Demand modelling
Advanced fraud
Automated valuation
Data platform
```


The intelligence roadmap may later add agentic capabilities above these services:

```text
Property assistant
Buyer assistant
Agent copilot
Verification assistant
Market intelligence agent
```

These agents orchestrate intelligence; they do not replace machine-learning predictions or authoritative marketplace workflows.

---

# 105A. Future Transaction Enablement

After the marketplace, trust and intelligence foundations are validated, Dwelvara may progressively support more of the property transaction lifecycle.

Potential future capabilities:

```text
Offers and negotiation
Transaction milestones
Professional-service integrations
Payment / escrow integrations where appropriate
Financing / mortgage integrations
Verified transaction outcomes
```

This is a future strategic direction, not an MVP commitment.

Node.js/TypeScript remains authoritative for transaction state, permissions, lifecycle transitions and consequential actions. Python intelligence may support these workflows with valuation, risk, recommendation, anomaly detection and explanations, but must not independently execute or approve transactions.

Verified transaction outcomes, when lawfully and reliably captured, may become high-value inputs to comparables, market intelligence and future automated valuation models.

### Future Ownership Confidence

Dwelvara may eventually support a post-transaction ownership-confidence layer where legally and operationally appropriate. Potential capabilities include ownership verification, title/document records, verified transaction completion, verified ownership transitions and property ownership history.

```text
Property
   ↓
Listing and asking-price history
   ↓
Verification history
   ↓
Verified transaction outcome
   ↓
Verified ownership transition
   ↓
Future resale / rental history
```

This is a long-term direction, not an MVP commitment. Dwelvara must not claim legal ownership or title status without appropriate authoritative evidence, professional/legal controls and jurisdiction-specific processes.

---

# 106. Architectural Guardrails

1. Do not hard-code Lagos into generic marketplace services.
2. Do not assume NGN.
3. Do not assume Nigerian address terminology.
4. Do not combine Property and Listing.
5. Do not reduce verification to a Boolean.
6. Do not confuse asking price and transaction price.
7. Do not rely on frontend-only permissions.
8. Do not expose private verification evidence.
9. AI must not invent property facts.
10. Do not create premature microservices.
11. Do not duplicate marketplace CRUD in Python.
12. Do not put analytical/ML logic into Node.js merely for convenience.
13. Do not allow Python to modify authoritative marketplace state without an ADR.
14. Do not make the marketplace dependent on Python availability.
15. Do not create separate repositories for Node.js and Python during MVP.
16. Do not allow TypeScript and Python contracts to drift independently.
17. Do not copy business rules between runtimes.
18. Do not introduce Kafka, Spark or similar infrastructure without demonstrated need.


Additional intelligence guardrails:

- Do not treat AI agents as replacements for machine-learning models.
- Do not allow AI agents to bypass intelligence services, shared contracts or Node.js marketplace authority.
- Do not present ML predictions or LLM-generated explanations as verified facts without appropriate provenance and uncertainty.

---

# 107. Technology Decision Rule

For every backend feature:

### Does it modify authoritative marketplace state?

Use:

```text
Node.js
```

### Is it primarily AI, analytics, statistics, ML or computation?

Use:

```text
Python
```

### Does it require both?

Use:

```text
Node.js
→ authentication
→ permissions
→ orchestration
→ authoritative persistence

Python
→ computation
→ analysis
→ intelligence
```

---

# 108. Architecture Validation

Before product implementation, confirm:

```text
Can one Property have multiple Listings?

Can a Property move between sale and rent?

Can Lagos and Abuja coexist?

Can London be added without changing Property?

Can one listing use NGN and another GBP?

Can areas use sqm and sqft?

Can verification rules differ by market?

Can Agent and Property verification remain separate?

Can verification evidence remain private?

Can asking price remain separate from transaction price?

Can Node.js operate if Python is unavailable?

Does every capability have one authoritative runtime?

Can Python outputs be versioned?

Can Node.js and Python validate shared contracts?

Can Python flag risk without executing moderation?

Can Python intelligence evolve without rewriting marketplace CRUD?

Can apps/web and apps/python-intelligence deploy independently?

Can both runtimes remain in one Git repository?
```

If any answer is no, revise the architecture.

---

# 109. MVP Definition of Done

The MVP is complete when this journey works:

```text
Agent registers
      ↓
Agent creates profile
      ↓
Agent submits verification
      ↓
Admin approves agent
      ↓
Agent creates Property
      ↓
Agent creates Listing
      ↓
Agent uploads media
      ↓
Agent optionally requests AI description
      ↓
Python generates draft
      ↓
Agent approves content
      ↓
Listing submitted
      ↓
Admin approves listing
      ↓
Listing published
      ↓
Seeker searches Lagos
      ↓
Seeker reviews verification
      ↓
Seeker saves listing
      ↓
Seeker creates enquiry
      ↓
Seeker requests viewing
      ↓
Agent receives lead
      ↓
Agent manages lead
      ↓
Trust score is generated
      ↓
Admin can review audit history
```

---

# 110. Architectural Definition of Done

The implementation must demonstrate:

```text
Lagos is configuration/reference data.

Nigeria is configuration/reference data.

NGN is configuration.

SQM is configuration.

Property is country-neutral.

Listing is country-neutral.

Node.js owns transactional state.

Python owns derived intelligence.

The repository is a monorepo.

apps/web is independently deployable.

apps/python-intelligence is independently deployable.

Python failure does not stop the marketplace.

Cross-runtime contracts are versioned.

Contract tests prevent Node.js/Python drift.

Abuja can be added through configuration/reference data.

A test London market works without redesigning core domains.
```

---

# 111. Repository Foundation Definition of Done

This section is the same Phase 0 definition of done defined in Section 97; it is not a second competing checklist.

The repository foundation must contain:

```text
dwelvara/
├── apps/
│   ├── web/                     ✓
│   └── python-intelligence/     ✓
├── packages/
│   ├── contracts/               ✓
│   ├── config/                  ✓
│   ├── database/                ✓
│   └── testing/                 ✓
├── infrastructure/              ✓
├── docs/                        ✓
├── scripts/                     ✓
├── .github/workflows/           ✓
├── docker-compose.yml           ✓
├── package.json                 ✓
├── pnpm-workspace.yaml          ✓
├── turbo.json                   ✓
├── .env.example                 ✓
├── AGENTS.md                    ✓
├── CLAUDE.md                    ✓
└── README.md                    ✓
```

Completion is determined exclusively by the Phase 0 checklist in Section 97.

A seed process for marketplace entities belongs to later phases after those entities exist; Phase 0 may contain only infrastructure/bootstrap seeds required to prove configuration or migration mechanics.

---

# 112. Product Architecture Summary

```text
                 DWELVARA
                            │
                ┌───────────┴───────────┐
                │                       │
                ▼                       ▼
        NODE.JS MARKETPLACE      PYTHON INTELLIGENCE
                │                       │
       ┌────────┼─────────┐      ┌──────┼────────────┐
       │        │         │      │      │            │
   Property  Listing  Verification AI  Trust       Analytics
       │        │         │      │      │            │
       └────────┴─────────┘      └──────┴────────────┘
                │                       │
                └───────────┬───────────┘
                            │
                         MARKET
                            │
             ┌──────────────┼──────────────┐
             │              │              │
         NG-LAGOS       NG-ABUJA       GB-LONDON
           MVP           FUTURE          FUTURE
```

Repository:

```text
                 ONE GIT REPOSITORY
                        │
           ┌────────────┴────────────┐
           │                         │
           ▼                         ▼
       apps/web            apps/python-intelligence
           │                         │
     Next.js/Node                FastAPI/Workers
           │                         │
           └────────────┬────────────┘
                        │
                Shared Contracts
                        │
              packages/contracts
```

---

# 113. Coding Agent Instructions

Dwelvara may be developed using Claude Code and OpenAI Codex.

Neither coding tool is the source of truth.

The authoritative hierarchy is:

```text
docs/product/product-specification.md
        ↓
accepted Architecture Decision Records
        ↓
active implementation plan
        ↓
AGENTS.md
        ↓
tool-specific instructions such as CLAUDE.md
```

Any coding agent working in this repository must:

1. read `AGENTS.md`;
2. read the relevant product specification sections;
3. read applicable ADRs;
4. read the active implementation plan for the milestone;
5. identify the current implementation phase before changing code;
6. preserve approved architecture and runtime ownership boundaries;
7. implement only the requested milestone;
8. run the required validation checks;
9. summarise changes and unresolved issues;
10. stop rather than automatically starting the next milestone.

The following remain fixed architectural decisions unless explicitly changed through an approved ADR:

```text
Monorepo from day one

apps/web
→ Next.js
→ React
→ TypeScript
→ Node.js transactional marketplace

apps/python-intelligence
→ Python
→ FastAPI
→ background workers
→ AI
→ trust
→ risk
→ analytics
→ property intelligence

packages/contracts
→ language-neutral Node.js/Python contracts

PostgreSQL
→ one operational database with marketplace and intelligence schemas

Prisma
→ marketplace schema migration authority

Alembic
→ intelligence schema migration authority

PostGIS
→ enabled from Phase 0

Redis
→ initial asynchronous transport
```

Node.js owns authoritative marketplace state and transactional workflows.

Python owns derived intelligence and computational workloads.

Do not create duplicate marketplace CRUD APIs in Python.

Do not place significant AI, ML or statistical modelling in Node.js merely for convenience.

Do not allow Python to directly alter authoritative marketplace state without an approved ADR.

The Node.js marketplace must continue operating when Python intelligence is unavailable.

Treat Property and Listing as separate domain entities.

Treat Market and Geography as first-class domains.

Do not hard-code:

```text
Lagos
Nigeria
NGN
+234
SQM
State
LGA
```

into generic marketplace logic.

Cross-runtime contracts must remain versioned and language-neutral.

For substantial work, the active coding agent should plan before implementation and state:

- objective;
- files to be changed;
- architecture implications;
- validation commands;
- completion criteria.

Do not run multiple coding agents against the same working tree concurrently.

When Claude Code and Codex are both used on the same milestone, nominate one as the primary implementation agent and the other as the review agent.

Parallel work is allowed only when isolated through separate Git branches or worktrees with clearly separated scopes.

See:

`docs/development/ai-agent-workflow.md`

for the detailed multi-agent workflow.

---

# 114. Final Engineering Rules

> **One product, one monorepo, independently deployable runtimes.**

> **Node.js runs the marketplace; Python makes the marketplace intelligent.**

> **Lagos is the first market, not the architecture.**

> **Project documentation and accepted ADRs are authoritative; coding agents are implementation tools, not sources of truth.**