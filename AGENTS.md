# fp-context — Agent Instructions

AI context hub for Team Foreldrepenger. Primary audience for content here is
AI assistants, not humans.

## When editing here

- Optimize for **AI consumption**: tables > bullets > code > prose
- Keep files within budgets in [CONTRIBUTING.md](CONTRIBUTING.md)
- Link to authoritative sources — do not embed them
- One topic per file; split rather than grow

## Adding files

| Directory | For |
|-----------|-----|
| `domain/` | Business rules, legal context, terminology |
| `architecture/` | System structure, tech stack, integration patterns |
| `conventions/` | Code style, workflow, testing approach |
| `operations/` | CI/CD, deployment, dependency management |

Update README.md index when adding a file.

## Verifying changes

- No build/test pipeline — markdown only
- After merge, the TeamForeldrepenger Copilot Space auto-syncs from main
- Validate by asking Copilot a question that should use the updated content

## Out of scope

| Topic | Belongs in |
|-------|-----------|
| Per-repo build/test commands | each repo's `.github/copilot-instructions.md` |
| Per-repo agent workflows | each repo's `AGENTS.md` |
| Workflow YAML | `fp-gha-workflows` |
| Dependency versions | `fp-bom` |
| Application code | the relevant repo |
| Folketrygdloven text | link out only |
