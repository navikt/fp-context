# Contributing to fp-context

Primary audience: **AI assistants**. Humans maintain, AI consumes.
Optimize for token-efficiency, retrievability, and factual density.

## Length budgets

| Document type | Target | Hard limit |
|--------------|--------|------------|
| Instructions / conventions | ≤100 lines | 150 lines |
| Reference (glossary, repo map) | ≤200 lines | 300 lines |
| Domain explainers | ≤120 lines | 180 lines |
| Per-repo summaries (if added) | ≤40 lines | 60 lines |

If a topic needs more length, **split it** into multiple focused files.

## Format hierarchy

Prefer formats higher on this list:

1. **Tables** — highest density, machine-parseable
2. **Bullet lists** — discrete facts
3. **Code blocks** — for commands and code patterns only
4. **Headers** — for retrieval anchors
5. **Prose** — only when nothing else fits

## Style rules

- **No prose intros** — start with content, skip "This document describes..."
- **No connectives** — drop "Note that", "It is important to", "As mentioned"
- **Link, don't quote** — reference Folketrygdloven, Nav docs, Aksel docs by URL
- **One topic per file** — small files compose better in retrieval
- **Examples minimal** — one clear example > three illustrative
- **Norwegian for domain terms, English for technical** — match the codebase
- **No marketing prose** — skip "this important repository handles..."

## File organization

| Directory | Content |
|-----------|---------|
| `domain/` | Business rules, legal context, terminology |
| `architecture/` | System structure, tech stack, integration patterns |
| `conventions/` | Code style, workflow, testing approach |
| `operations/` | CI/CD, deployment, dependency management |

Update README.md index when adding a new file.

## Out of scope

| Topic | Belongs in |
|-------|-----------|
| Per-repo build/test commands | each repo's `.github/copilot-instructions.md` |
| Per-repo agent workflows | each repo's `AGENTS.md` |
| Workflow YAML, composite actions | `fp-gha-workflows` |
| Dependency and plugin versions | `fp-bom` |
| Application code, tests, business rules | the relevant app/library repo |
| Folketrygdloven text, Nav circulars | link out — do not embed |

## Workflow

Standard team flow:
1. Branch from `main`
2. Edit (within budgets)
3. PR with description of what and why
4. One reviewer
5. Squash-merge

After merge, the TeamForeldrepenger Copilot Space auto-syncs.

## Review checklist

- [ ] Within length budget
- [ ] Tables/bullets used where possible (prose minimized)
- [ ] No duplication of existing content
- [ ] External authoritative sources linked, not embedded
- [ ] Norwegian/English usage consistent with codebase
- [ ] README.md index updated if file added
