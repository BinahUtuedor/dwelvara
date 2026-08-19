# Dwelvara AI Agent Development Workflow

## Purpose

Dwelvara may be developed using both Claude Code and OpenAI Codex.

The purpose of this workflow is to gain the benefits of multiple coding agents without allowing them to create conflicting architecture, duplicate work or overwrite each other's changes.

Neither Claude Code nor Codex is the source of truth.

The source of truth is the repository documentation and accepted Architecture Decision Records.

## Authority Hierarchy

```text
docs/product/product-specification.md
        ↓
accepted ADRs
        ↓
active implementation plan
        ↓
AGENTS.md
        ↓
tool-specific instructions
```

`CLAUDE.md` contains Claude Code-specific behaviour.

Codex follows repository guidance in `AGENTS.md`.

## Coding Agents vs Product AI Agents

Do not confuse development-time coding agents with Dwelvara runtime AI agents.

```text
Claude Code / OpenAI Codex
→ development tools that plan, implement and review repository changes

Dwelvara AI agents
→ runtime product components inside apps/python-intelligence
```

Dwelvara runtime agents may orchestrate approved intelligence services and tools for user or operational tasks. They do not replace machine-learning models. ML models continue to provide predictions, scores and rankings; intelligence services combine those outputs with other evidence; runtime AI agents orchestrate those services.

Coding-agent workflow rules in this document do not define runtime agent architecture. Runtime intelligence architecture is defined in:

`docs/architecture/intelligence-layer.md`

## Roles Per Milestone

For each milestone, nominate:

```text
Primary implementation agent
Review agent
```

Either tool may perform either role.

Examples:

```text
Claude Code
→ primary implementation

Codex
→ independent review
```

or:

```text
Codex
→ primary implementation

Claude Code
→ independent review
```

Do not assign both agents as simultaneous editors of the same working tree.

## Before Implementation

The primary implementation agent must:

1. read `AGENTS.md`;
2. read its tool-specific instructions where applicable;
3. read the relevant sections of the product specification;
4. read applicable ADRs;
5. read the active implementation plan;
6. inspect the current code;
7. identify the current project phase;
8. state the intended scope;
9. identify files expected to change;
10. identify tests/checks required.

For substantial work, plan before modifying files.

## Implementation Rules

Only one coding agent should modify a given working tree at a time.

The implementation agent should:

1. implement the smallest complete increment;
2. preserve runtime and database ownership;
3. avoid unrelated refactoring;
4. update tests with behaviour changes;
5. update documentation when architecture changes;
6. run the required validation commands;
7. leave the branch in a reviewable state.

Do not automatically start the next milestone.

## Review Rules

The review agent should review:

- the Git diff;
- product specification compliance;
- ADR compliance;
- Node.js/Python ownership boundaries;
- database ownership;
- contract compatibility;
- security;
- test coverage;
- error handling;
- failure isolation;
- unnecessary complexity.

The review agent should classify findings as:

```text
Critical
High
Medium
Low
Suggestion
```

The review agent should explain why each finding matters.

The review agent must not automatically rewrite the primary agent's implementation unless explicitly asked.

Style preferences should not be presented as architecture defects.

## Architecture Changes

Neither agent may silently change:

- monorepo structure;
- Node.js/Python ownership;
- marketplace/intelligence database schema ownership;
- Property/Listing separation;
- Market/Geography design;
- Verification architecture;
- cross-runtime contract strategy;
- Redis communication model;
- multi-market requirements.

If an implementation exposes a need to change one of these:

```text
Agent identifies issue
        ↓
Agent explains proposed change
        ↓
Human approval
        ↓
ADR created/updated where appropriate
        ↓
Implementation proceeds
```

## Parallel Work

Parallel work is allowed only when isolated.

Use separate Git branches or worktrees.

Example:

```text
main

├── worktree/phase-1-market
│   └── Claude Code
│
└── worktree/phase-1-auth
    └── Codex
```

Parallel tasks must have clearly separated file/domain ownership.

Do not assign overlapping database migrations or shared-contract changes to separate agents at the same time unless explicitly coordinated.

## Recommended Workflow

For most milestones:

```text
1. Human selects milestone
        ↓
2. Primary agent creates/reviews plan
        ↓
3. Human approves scope
        ↓
4. Primary agent implements
        ↓
5. Primary agent runs tests/checks
        ↓
6. Review agent inspects diff
        ↓
7. Human evaluates review findings
        ↓
8. Primary agent fixes accepted findings
        ↓
9. Final checks
        ↓
10. Git commit
```

## Example: Claude Implements, Codex Reviews

Claude Code instruction:

```text
Read AGENTS.md, CLAUDE.md, the relevant product specification sections,
ADRs and active implementation plan.

Implement only the approved milestone.

Run all required validation checks.

Do not begin the next milestone.

At the end provide a handoff containing files changed, commands run,
test results and unresolved issues.
```

Codex review instruction:

```text
Read AGENTS.md, the relevant product specification sections, ADRs and
active implementation plan.

Review the current Git diff only.

Do not modify files.

Check architecture compliance, runtime ownership, database ownership,
contracts, security, tests and unnecessary complexity.

Rank findings by severity and explain each finding.

Do not treat optional style preferences as defects.
```

## Example: Codex Implements, Claude Reviews

Codex implementation instruction:

```text
Read AGENTS.md, the relevant product specification sections, ADRs and
active implementation plan.

Implement only the approved milestone.

Preserve all architecture and ownership boundaries.

Run required lint, typecheck, tests and builds.

Stop after the milestone and provide a handoff.
```

Claude Code review instruction:

```text
Remain in review mode.

Read AGENTS.md, CLAUDE.md, the relevant product specification sections,
ADRs and active implementation plan.

Review the Git diff without modifying files.

Identify specification deviations, architecture violations, security
issues, missing tests and unnecessary complexity.

Rank findings by severity.
```

## Handoff Format

Every implementation handoff should include:

```text
Milestone
Agent
Branch/worktree
Files changed
Migrations created
Contracts changed
Commands run
Lint result
Typecheck result
Test result
Build result
Known issues
Architecture decisions made
Review status
Next recommended step
```

If no architecture decision was made, state:

```text
Architecture decisions made: None
```

## Conflict Resolution

If Claude Code and Codex disagree:

1. compare both recommendations with the product specification;
2. compare with accepted ADRs;
3. prefer the simplest approach that satisfies the requirements;
4. do not resolve disagreement by allowing both implementations;
5. document material architectural decisions in an ADR;
6. obtain human approval before changing settled architecture.

## Phase Discipline

Both agents must respect the implementation roadmap.

In particular, Phase 0 is foundation only.

Phase 0 must not implement:

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

Later-phase functionality must not be introduced merely because an agent considers it convenient.

## Final Rule

> Coding agents are collaborators, not sources of truth.

> One agent implements a given working tree at a time.

> The second agent is most valuable as an independent reviewer.
