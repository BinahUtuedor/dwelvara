# Dwelvara — Claude Code Instructions

This file contains Claude Code-specific operating instructions.

Repository-wide engineering rules are defined in:

`AGENTS.md`

The authoritative product specification is:

`docs/product/product-specification.md`

Relevant architecture decisions are under:

`docs/architecture/decisions/`

Read `AGENTS.md` and the relevant repository documentation before substantive work.

## Instruction Precedence

Use:

```text
Product specification
        ↓
Accepted ADRs
        ↓
Active implementation plan
        ↓
AGENTS.md
        ↓
CLAUDE.md
```

Do not redefine shared engineering rules in this file.

## Claude Code Workflow

For substantial or architectural changes:

1. use Plan mode first;
2. read the relevant product specification and ADRs;
3. inspect the existing implementation;
4. present the proposed implementation plan;
5. identify files that will change;
6. identify architecture implications;
7. wait for approval where the user has requested plan review;
8. implement only the approved milestone;
9. run linting, type checking, tests and relevant builds;
10. fix failures;
11. summarise the changes;
12. stop.

Do not automatically begin the next milestone.

## Scope Discipline

Always identify the active implementation phase before modifying files.

Do not implement features from future phases unless explicitly requested.

During Phase 0, do not implement marketplace domains such as:

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

Phase 0 is architecture and monorepo foundation only.

## Architecture Preservation

Preserve the repository rules in `AGENTS.md`, especially:

- monorepo from day one;
- `apps/web` for Next.js/Node.js/TypeScript;
- `apps/python-intelligence` for FastAPI/Python/workers;
- Node.js owns authoritative marketplace state;
- Python owns the Dwelvara Intelligence Platform;
- ML models produce predictions/scores/rankings inside the intelligence layer;
- intelligence services combine predictions with rules, analytics and marketplace facts;
- runtime AI agents orchestrate approved intelligence services/tools and do not replace ML models;
- `marketplace` schema belongs to Prisma/Node.js;
- `intelligence` schema belongs to Alembic/Python;
- Python must not become a second CRUD backend;
- Property and Listing are separate;
- Lagos is configuration, not architecture;
- cross-runtime contracts are versioned and language-neutral;
- the marketplace must remain functional when Python is unavailable.

Do not silently alter these decisions.

Read `docs/architecture/intelligence-layer.md` before substantial AI, ML, analytics, scoring, recommendation or runtime-agent work.

## Working with Codex

Dwelvara may use OpenAI Codex as well as Claude Code.

Do not assume Claude Code is the sole implementation agent.

When Claude Code is the primary implementation agent:

- implement only the assigned scope;
- leave the working tree in a reviewable state;
- provide a concise handoff for Codex or human review.

When Claude Code is the review agent:

- inspect the relevant diff and tests;
- report findings by severity;
- do not rewrite the implementation unless explicitly asked;
- distinguish architecture violations from optional style preferences.

Do not edit the same working tree concurrently with another coding agent.

Use separate Git branches/worktrees for parallel work.

See:

`docs/development/ai-agent-workflow.md`

## Destructive Commands

Before running destructive or difficult-to-reverse commands:

- explain exactly what the command does;
- explain why it is necessary;
- avoid deleting user work;
- request explicit approval where appropriate.

## Communication

The user is learning coding-agent workflows.

Explain important commands and architectural decisions clearly.

Avoid unnecessary jargon and unnecessary infrastructure.

When work completes, report:

- files changed;
- commands run;
- validation results;
- unresolved issues;
- next recommended milestone.

Then stop.
