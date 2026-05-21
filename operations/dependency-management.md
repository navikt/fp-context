# Dependency Management

## fp-bom — parent POM

All Java repos inherit from fp-bom. Centralizes Java version, Maven plugin
versions, test dependencies, SonarCloud config, build profiles.

## Current versions (fp-bom)

| Dependency | Version |
|-----------|---------|
| Java | 25 |
| JUnit | 6.0.3 |
| AssertJ | 3.27.7 |
| Mockito | 5.23.0 |
| JaCoCo | 0.8.14 |
| Maven Surefire | 3.5.5 |
| Sonar Plugin | 5.6.0.6792 |

Framework versions (Jetty, Jersey, Weld, Hibernate, Jackson) managed via
fp-felles BOMs, not directly in fp-bom.

## Dependabot

- Enabled on all repos
- Review and merge promptly
- Fix or document blockers — don't let updates pile up

## Update ripple effects

| Library | Consumers |
|---------|-----------|
| fp-bom | All Java repos |
| fp-felles | All backend apps |
| fp-prosesstask | All backend apps |
| fp-kontrakter | Multiple apps |
| fp-nare | Business rule repos + fp-sak |
| fp-tidsserie | fp-sak, fp-uttak, others |
| fp-uttak / fp-inngangsvilkar | fp-sak (SemVer) |

## Update protocol

1. Change in library repo, PR + merge to main
2. SNAPSHOT updates auto-flow to downstream
3. For SemVer libraries: release, then update consumer pom.xml
4. Verify downstream CI is green before broad rollout

## Strategy

- Stay current — Dependabot drives the cadence
- Don't pin old versions without documented reason
- Use Java preview features selectively when value is clear
