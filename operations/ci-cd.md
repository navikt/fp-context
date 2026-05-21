# CI/CD

All repos use reusable workflows from [fp-gha-workflows](https://github.com/navikt/fp-gha-workflows).

## Reusable workflows

| Workflow | Purpose |
|----------|---------|
| `build-docker-bake.yml` | Maven build + Docker image + tests + deploy |
| `build-app-no-db.yml` | Static code analysis |
| `build-feature.yml` | Build feature branch (no deploy) |
| `deploy.yml` | Deploy to environment |
| `codeql.yml` | CodeQL security scanning |
| `deploy-storybook.yml` | Storybook deploy (frontend) |
| `mvn-dependency-submission.yml` | Submit Maven dep graph to GitHub |
| `release-drafter.yml` | Auto-draft release notes |
| `release-feature.yml` | Release from feature branch |

## Composite actions

| Action | Purpose |
|--------|---------|
| `build-maven-application` | Maven build with caching |
| `build-push-docker-image` | Build + push Docker image |
| `build-push-docker-bake` | Build + push via Docker Bake |
| `knip-it` | Frontend unused export detection |
| `setup-npmrc` | npm registry config |
| `setup-yarnrc` | Yarn registry config |

## Usage pattern

```yaml
jobs:
  build:
    uses: navikt/fp-gha-workflows/.github/workflows/build-docker-bake.yml@main
```

## SonarCloud

| Setting | Value |
|---------|-------|
| Organization | `navikt` |
| Project key | `navikt_<reponame>` (in repo pom.xml) |
| Gate | Must pass before merge |

## Deployment

| Aspect | Value |
|--------|-------|
| Platform | NAIS (Kubernetes) |
| Image registry | Google Artifact Registry |
| Image tag | Commit SHA |
| Environments | dev-gcp/dev-fss, prod-gcp/prod-fss |
| Config | `.nais/` in each app repo or fp-iac |
| Base images | fp-baseimages |
