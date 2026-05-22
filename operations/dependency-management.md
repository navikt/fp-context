# Dependency Management

Process and ownership for Java dependencies across all fp-repos. **Versions
themselves live in [`fp-bom`](https://github.com/navikt/fp-bom)** — this
document does not pin numbers.

## Source of truth

| What | Where |
|------|-------|
| Authoritative version pins | [`fp-bom/fp-bom/pom.xml`](https://github.com/navikt/fp-bom/blob/main/fp-bom/pom.xml) and [`fp-bom/pom.xml`](https://github.com/navikt/fp-bom/blob/main/pom.xml) |
| fp-bom scope, conventions, what's in/out | [`fp-bom/.github/copilot-instructions.md`](https://github.com/navikt/fp-bom/blob/main/.github/copilot-instructions.md) |
| Consumer view (which library to use from where) | [`architecture/team-libraries.md`](../architecture/team-libraries.md) |
| GitHub Actions pinning policy | [`operations/ci-cd.md`](ci-cd.md#workflow-pinning--ratchet-policy) |

All Java repos inherit from `fp-parent-app` (apps) or `fp-parent-lib`
(libraries). Both import the `fp-bom` BOM. Do **not** pin or override versions
in downstream repos.

## Dependabot

- Enabled on all repos for the `maven` and `github-actions` ecosystems
- Drives the upgrade cadence — review and merge promptly
- Fix or document blockers; don't let updates pile up
- GitHub Actions updates follow the ratchet policy in
  [`operations/ci-cd.md#workflow-pinning--ratchet-policy`](ci-cd.md#workflow-pinning--ratchet-policy)

## Update ripple effects

| Library | Consumers |
|---------|-----------|
| `fp-bom` | All Java repos |
| `fp-felles` | All backend apps |
| `fp-prosesstask` | All backend apps |
| `fp-kontrakter` | Multiple apps |
| `fp-nare` | Business rule repos + `fp-sak` |
| `fp-tidsserie` | `fp-sak`, `fp-uttak`, others |
| `fp-uttak` / `fp-inngangsvilkar` | `fp-sak` (SemVer) |

## Update protocol

1. Change in library repo, PR + merge to main
2. SNAPSHOT updates auto-flow to downstream
3. For SemVer libraries: release, then update consumer `pom.xml`
4. Verify downstream CI is green before broad rollout

## Strategy

- `fp-bom` is the single source of truth — never pin a version locally
- Stay current — Dependabot sets the cadence
- Don't hold back upgrades without a documented reason
- Use Java preview features selectively, when value is clear
