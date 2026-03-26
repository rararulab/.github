# rararulab — Organization Agent Guidelines

## Purpose
Central governance repo for rararulab. Files here are inherited by all repos in the org.

## Structure
- `CLAUDE.md` — Org-wide AI agent baseline rules
- `docs/` — Shared development guides (workflow, code style, commit conventions)
- `ISSUE_TEMPLATE/` — Inherited issue templates for all repos
- `pull_request_template.md` — Inherited PR template
- `CONTRIBUTING.md` — Inherited contribution guidelines
- `profile/README.md` — Org profile page

## Key Invariants
- Issue templates use a generic component dropdown — repos override with their own if needed
- All docs are in English
- CLAUDE.md is the baseline; individual repos extend it with repo-specific rules

## What NOT To Do
- Do NOT add repo-specific rules here — those belong in each repo's own CLAUDE.md
- Do NOT remove templates without checking which repos depend on inheritance
