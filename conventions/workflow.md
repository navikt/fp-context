# Workflow

## Branching

- Trunk-based: short-lived feature branches off `main`
- No long-lived dev or release branches
- `main` is always deployable

## Pull requests

- All changes via PR (no direct push to `main`)
- ≥1 reviewer
- Descriptive title (becomes the squash-merge commit)
- Keep PRs focused and small

## CI/CD pipeline

| Step | Tool |
|------|------|
| Build + unit tests | Maven |
| Static analysis | SonarCloud (org: navikt) |
| Docker image | fp-gha-workflows + fp-baseimages |
| Integration tests | fp-autotest (suite depends on app) |
| Deploy dev | Automatic on green build |
| Deploy prod | Automatic after dev succeeds |

## Versioning

| Repo type | Strategy |
|-----------|----------|
| Application | Trunk-based, no version (deploy latest) |
| Library (fp-felles, fp-prosesstask, fp-nare, fp-tidsserie) | SNAPSHOT on main, release on demand |
| Business rule (fp-uttak, fp-inngangsvilkar, fp-stonadskonto, etc.) | SemVer — major bump for breaking |

## Dependencies

- fp-bom as parent POM (versions centralized)
- Dependabot enabled on all repos
- Merge Dependabot PRs promptly
- When updating a shared library, verify downstream consumers
