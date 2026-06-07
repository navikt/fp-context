# Copilot instructions template

Template for `.github/copilot-instructions.md` in spoke repos.
Audience: LLMs and experienced developers — token-efficient, no duplication of hub content.

## Conventions

- Backticks for all identifiers: repo names, class names, module names
- Repo references: plain name (`fp-sak`), no `navikt/` prefix — the shared context block establishes the org
- Exception: hub reference in Shared context keeps `navikt/fp-context` as fully-qualified anchor
- Non-navikt systems (Joark, PDL, SAF, EREG) use their own names without prefix
- Monorepo consumers: `foreldrepengesoknad`/`planlegger` (repo/app)

## App template

```markdown
# {repo}

{one-line role description}

## Shared context

- Source of truth for shared domain, architecture, and conventions: `navikt/fp-context`
- Copilot Space: `navikt/TeamForeldrepenger`

## Repo-specific context

| Topic | Details |
|---|---|
| Role | {what this app does — one sentence} |
| Consumers | {who calls this app} |
| Tech stack | {e.g. Standard fp Java backend using `fp-prosesstask`} |
| Main integrations | {outbound dependencies — or directed list below for complex apps} |
| Data | {DB, storage, caching — or "Stateless; no persistence"} |

{optional 1-3 sentences: auth boundaries, special processing, domain-flow details}

## Entry points

{REST classes and/or Kafka/MQ consumers with short purpose description}

## Verification

{how to verify changes — fp-autotest suite, consuming flows, etc.}
```

## Library template

```markdown
# {repo}

{one-line role description}

## Shared context

- Source of truth for shared domain, architecture, and conventions: `navikt/fp-context`
- Copilot Space: `navikt/TeamForeldrepenger`

## Repo-specific context

| Topic | Details |
|---|---|
| Role | {what the library provides} |
| Tech stack | {e.g. Maven plugin + Java library} |
| Modules | {only if multi-module — list module names} |
| Consumers | {who depends on this — blast radius} |
| Key constraints | {non-obvious design choices, compatibility requirements} |

## Verification

{how to verify — local builds in consumers, fp-autotest suite, etc.}
```

## Guidance

| Concern | Approach |
|---------|----------|
| Large apps (fp-sak) | Add `## Repo structure` table for non-obvious module names; use directed integration list (Upstream/Satellites/Downstream) instead of flat `Main integrations` row; add glossary line for domain terms appearing in code |
| Entry points | List actual class names — REST resources, Kafka consumers/producers, MQ listeners. Avoid meta-instructions ("look in ApiConfig"); prefer concrete names and purpose |
| Length | Spoke files ≤40 lines target; large apps may go up to 60 lines |
| What NOT to include | Conventions, code style, workflow, tech stack details — all in `fp-context`. Package/directory listings that are self-descriptive or trivially discoverable |
