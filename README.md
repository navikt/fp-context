# fp-context

AI context hub for Team Foreldrepenger (`@navikt/teamforeldrepenger`).

Provides shared grounding for AI assistants across 40+ repos. Attached to the
[TeamForeldrepenger Copilot Space](https://github.com/copilot/spaces/navikt/15)
and referenced from each repo's `.github/copilot-instructions.md`.

## Index

| Path | Topic |
|------|-------|
| [domain/business-context.md](domain/business-context.md) | Folketrygdloven kap. 14, value chain, key concepts |
| [domain/glossary.md](domain/glossary.md) | Norwegian domain terms → code concepts |
| [architecture/system-overview.md](architecture/system-overview.md) | Repos by tier, data flow, deployment |
| [architecture/backend-stack.md](architecture/backend-stack.md) | Jetty, Jersey, Weld, Hibernate, Jackson |
| [architecture/frontend-stack.md](architecture/frontend-stack.md) | React, Aksel, React Hook Form, TanStack |
| [conventions/java-codestyle.md](conventions/java-codestyle.md) | Functional, fluent, Java 25+ |
| [conventions/workflow.md](conventions/workflow.md) | Trunk-based development, PRs, CI/CD |
| [conventions/testing.md](conventions/testing.md) | Unit (per repo) + integration (fp-autotest) |
| [operations/ci-cd.md](operations/ci-cd.md) | fp-gha-workflows, SonarCloud, NAIS |
| [operations/dependency-management.md](operations/dependency-management.md) | fp-bom, Dependabot |

## Usage

| Surface | How |
|---------|-----|
| Copilot Space (chat, IDE agent, CLI) | "Using the TeamForeldrepenger space owned by navikt, ..." |
| Direct reference from any repo | Link to `github.com/navikt/fp-context/...` from `copilot-instructions.md` or `AGENTS.md` |
| Other AI tools (Claude Projects, custom GPTs) | Add this repo as a source / paste URL |

## Editing

See [CONTRIBUTING.md](CONTRIBUTING.md). TL;DR: keep files concise, fact-dense,
table-first. Primary audience is AI, not humans.

## Companion hubs

Team Foreldrepenger AI context is split across three repos. **fp-context** is
the navigational hub and policy owner; the others own their domain content.
Downstream repos can point at fp-context alone — it routes onward.

| Role | Hub | Owns |
|------|-----|------|
| Navigational hub + cross-cutting policy | **fp-context** *(this)* | Domain, architecture, conventions, workflow pinning policy, dependency strategy |
| GitHub Actions content | [fp-gha-workflows](https://github.com/navikt/fp-gha-workflows) | Reusable workflows + composite actions catalog |
| Java dependency content | [fp-bom](https://github.com/navikt/fp-bom) | Version pins (Jakarta EE 11 platform), parent POMs |

Other shared-code hubs:

| Hub | Scope |
|-----|-------|
| [fp-felles](https://github.com/navikt/fp-felles) | Common Java building blocks (see [architecture/team-libraries.md](architecture/team-libraries.md)) |
| [fp-autotest](https://github.com/navikt/fp-autotest) | Integration tests for backend apps |
