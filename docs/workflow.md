# Development Workflow — Issue → Worktree → PR → Merge

**Every code change — no matter how small — MUST follow this workflow.** There are zero exceptions: single-line fixes, typo corrections, config tweaks, doc updates, and refactors all go through issue + worktree + PR. The main agent must NEVER directly edit source files on the `main` branch.

```
1. CREATE ISSUE    →  gh issue create + labels
2. CREATE WORKTREE →  git worktree add .worktrees/issue-{N}-{name} -b issue-{N}-{name}
3. WORK            →  All edits happen inside the worktree
4. VERIFY          →  Build + lint checks on worktree
5. PUSH & PR       →  git push -u origin + gh pr create
6. CLEANUP         →  git worktree remove + git branch -d (after PR merged)
```

## Step 1: Create Issue

Issues MUST use the GitHub issue templates defined in `.github/ISSUE_TEMPLATE/`. Pick the template matching the change type:

| Template | Use when |
|----------|----------|
| `feature_request.yml` | New feature or enhancement |
| `bug_report.yml` | Bug fix |
| `refactor.yml` | Code refactor or technical improvement |
| `chore.yml` | CI, dependencies, tooling, maintenance |

```bash
# Example: feature request
gh issue create --template feature_request.yml \
  --title "feat(scope): short description" \
  --body "$(cat <<'EOF'
### Description
What you want and why.

### Component
core (runtime, engine, core logic)

### Alternatives considered
None.
EOF
)" --label "agent:claude" --label "core"

# Example: bug report
gh issue create --template bug_report.yml \
  --title "fix(scope): short description" \
  --body "$(cat <<'EOF'
### Description
What happened and what you expected.

### Component
backend (API, server, services)

### Steps to reproduce
1. ...
2. ...

### Logs / Error output
...
EOF
)" --label "agent:claude" --label "backend"
```

**Issue Labels** (all issues MUST have proper labels):
- **Agent** (required for agent-created issues): `agent:claude`, `agent:codex` — use the label matching the agent performing the operation
- **Type**: auto-applied by the template (`enhancement`, `bug`, `refactor`, `chore`)
- **Component** (pick one): `core`, `backend`, `frontend`, `cli`, `ci`, `docs`

## Step 2: Create Worktree
```bash
git worktree add .worktrees/issue-{N}-{short-name} -b issue-{N}-{short-name}
```

## Step 3: Work in Worktree
- **All code edits happen exclusively inside the worktree directory** — never in the main checkout
- The main agent may dispatch a subagent to the worktree, or work there directly
- Independent issues can be dispatched **in parallel** (each in its own worktree)
- All work should be committed before moving to the next step

## Step 4: Verify Builds
After work is complete, verify in the worktree. The exact commands depend on the project:
```bash
# Rust projects
cargo check -p {crate-name}
cargo clippy --workspace --all-targets --all-features --no-deps -- -D warnings

# Frontend projects
npm run build
npm run lint

# Or use the project's task runner (just, make, etc.)
```

## Pre-commit Checks

If the project uses pre-commit hooks (e.g., [prek](https://github.com/j178/prek)), the **final commit** in any PR must pass all checks — intermediate commits during development don't need to pass.

If pre-commit hook blocks a commit during development, fix issues before the final commit. Do NOT use `--no-verify` to skip hooks.

## Step 5: Push & Create PR

PRs use the template at `.github/pull_request_template.md`. Fill in all sections.

```bash
git push -u origin issue-{N}-{short-name}
gh pr create --title "fix(scope): description (#N)" --body "$(cat <<'EOF'
## Summary

Brief description of the changes.

## Type of change

| Type | Label |
|------|-------|
| Bug fix | `bug` |

## Component

`core`

## Closes

Closes #N

## Test plan

- [x] Tests pass
- [x] Lints pass
- [x] Tested locally
EOF
)" --label "bug" --label "core"
```
- Commit message must include `Closes #N` so the issue is auto-closed when PR merges
- Never merge locally — all merges happen through GitHub PR
- **PR Labels** (all PRs MUST have proper labels):
  - **Type** (pick one): `bug`, `enhancement`, `refactor`, `chore`, `documentation`
  - **Component** (pick one): `core`, `backend`, `frontend`, `cli`, `ci`, `docs`

## Step 5.5: Wait for CI Green (MANDATORY)

After creating the PR, **you MUST verify that all CI checks pass before reporting completion to the user.**

```bash
gh pr checks {PR-number} --watch    # Wait for all checks to complete
```

- If any check fails, investigate and fix in the worktree, push again, and re-verify
- Do NOT report "PR created" or "task done" to the user while CI is still pending or failing
- Only after all checks are green may you inform the user that the PR is ready

## Step 6: Cleanup (after PR merged)
```bash
git worktree remove .worktrees/issue-{N}-{short-name}
git branch -d issue-{N}-{short-name}
```

## Parallel Execution

When user requests involve multiple independent changes, split into separate issues and dispatch subagents in parallel:
- Each subagent gets its own worktree, branch, and PR
- PRs are reviewed and merged independently on GitHub
