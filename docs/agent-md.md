# AGENT.md Requirements

Every crate and significant module directory MUST have an `AGENT.md` file that guides AI agents working in that area. This is mandatory for new crates and expected to be added incrementally for existing ones.

## When to Create

- **New crate/package**: `AGENT.md` is required at the package root before the PR can merge
- **New major module**: If a subdirectory has its own domain logic, add an `AGENT.md` there
- **Significant refactor**: If you restructure internals, update or create the `AGENT.md`

## Template

```markdown
# {package-name} — Agent Guidelines

## Purpose
One sentence: what this package does and why it exists.

## Architecture
Key modules, data flow, and public API surface. Point to real source files rather than abstract descriptions.

## Critical Invariants
Constraints that MUST NOT be violated (thread safety, ordering guarantees, security boundaries).
Explain the consequence of violation.

## What NOT To Do
Explicit anti-patterns with reasoning. Format: "Do NOT X — because Y".

## Dependencies
Upstream/downstream package relationships and external service dependencies.
```

## Rules

- Keep each `AGENT.md` under 300 lines — only include what an agent cannot infer from reading the code
- Write in English
- Executable commands and real file paths over abstract descriptions
- Update `AGENT.md` in the same PR when you change the package's architecture or invariants
- Do NOT let AI auto-generate `AGENT.md` from scratch — the author (human or agent who built the feature) writes it based on actual design decisions
