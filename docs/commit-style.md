# Commit Style — Conventional Commits (MANDATORY)

Every commit message MUST follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <description> (#N)

<optional body>

Closes #N
```

- **Allowed types**: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `ci`, `perf`, `style`, `build`, `revert`
- **Scope** matches module or area: `feat(core):`, `fix(api):`, `refactor(auth):`
- **Breaking changes** use `!`: `feat(api)!: remove deprecated endpoint`
- Include `(#N)` issue reference in commit subject
- Include `Closes #N` in commit body
- If the project has a `commit-msg` hook, do NOT bypass it
- Do NOT use free-form commit messages like `"update code"` or `"fix stuff"` — they will be rejected
