# CI/CD

All repos use reusable workflows and composite actions from
[fp-gha-workflows](https://github.com/navikt/fp-gha-workflows). See its
[README](https://github.com/navikt/fp-gha-workflows#readme) for the current
catalog and usage examples — not duplicated here.

## Repos with own CI

Most repos compose from `fp-gha-workflows`. A few maintain workflow logic of
their own:

| Repo | Reason |
|------|--------|
| `fp-gha-workflows` | Hosts the reusable workflows themselves |
| `fp-baseimages` | Matrix build of base images; bespoke release flow |
| `fp-iac` | Matrix deploy of infrastructure (alerts, Kafka, Redis) per cluster |
| `fp-autotest` | Scheduled cross-app integration test runs + reporting |

All foreldrepenger-/fp-repos should compose workflows using from `fp-gha-workflows` 
where possible and only add local actions and dependencies to external actions for local needs. 

The pinning policy below applies regardless.

## Workflow pinning & ratchet policy

Canonical home for this policy. Applies to **every workflow file** in every
fp-repo — reusable, local, or composed.

1. External actions → full commit SHA + ratchet comment with semver:
   `actions/checkout@de0fac2…ce83dd # ratchet:actions/checkout@v6.0.2`
2. External action that must track a mutable tag → `@vX.Y.Z # ratchet:exclude`
   (justify in PR). Example: `nais/attest-sign@v2.0.8`.
3. Internal `navikt/fp-gha-workflows/...` → `@main # ratchet:exclude`
   (consumers track main).
4. Never branch-ref any other repo.
5. Dependabot (`github-actions` ecosystem) is the primary source of external
   updates and respects the ratchet comments above.

## SonarCloud

| Setting | Value |
|---------|-------|
| Organization | `navikt` |
| Project key | `navikt_<reponame>` (in repo `pom.xml`) |
| Gate | Must pass before merge |

## Deployment

| Aspect | Value |
|--------|-------|
| Platform | NAIS (Kubernetes) |
| Image registry | Google Artifact Registry |
| Image tag | Commit SHA |
| Environments | dev-gcp/dev-fss, prod-gcp/prod-fss |
| Config | `.nais/` in each app repo or `fp-iac` |
| Base images | `fp-baseimages` |
