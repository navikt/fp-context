# Contributing to fp-context

Audience: **AI assistants and experienced developers** — keep short and precise.
Optimize for token efficiency, retrievability, and factual density.

## Length budgets

| Document type | Target | Hard limit |
|--------------|--------|------------|
| Instructions / conventions | ≤100 lines | 150 lines |
| Reference (glossary, repo map) | ≤200 lines | 300 lines |
| Domain explainers | ≤120 lines | 180 lines |
| Per-repo summaries (if added) | ≤40 lines | 60 lines |

Split rather than grow.

## Format & style

Format — minimize tokens while preserving meaning:
- **Bullets** — key→value pairs, simple enumerations, discrete facts, ≤~6 items
- **Tables** — multi-column data, or lists longer than 6 items
- **Code blocks** — commands and code only
- **Headers** — retrieval anchors; skip for single-item sections
- **Prose** — only when structure would obscure meaning

Style:
- Start with content — no "This document describes..."
- No connectives: drop "Note that", "It is important to"
- Link, don't quote — Folketrygdloven, Nav docs, Aksel docs by URL
- One topic per file — small files compose better in retrieval
- One example max where needed
- Norwegian for domain terms, English for technical — match the codebase

## File organization

- `domain/` — Business rules, legal context, terminology
- `architecture/` — System structure, tech stack, integration patterns
- `conventions/` — Code style, workflow, testing approach
- `operations/` — CI/CD, deployment, dependency management

Update README.md and llms.txt index when adding a file.

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

Branch → edit (within budget) → PR (1 reviewer) → squash-merge. Space auto-syncs after merge.

## Review checklist

- [ ] Within length budget
- [ ] Tables/bullets used where possible (prose minimized) and matching content type per rules above
- [ ] No duplication of existing content
- [ ] External sources linked, not embedded
- [ ] Norwegian/English usage consistent with codebase
- [ ] README.md index updated if file added
