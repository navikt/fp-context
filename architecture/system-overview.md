# System Overview

## Repos by tier

### Tier 1 — Foundations

| Repo | Purpose |
|------|---------|
| fp-bom | Parent POM, Java/plugin/dependency versions |
| fp-gha-workflows | Reusable GitHub Actions workflows + composite actions |
| fp-baseimages | Base Docker images for Java apps |
| fp-iac | Infrastructure as code (NAIS, alerts) |

### Tier 2 — Common libraries

| Repo | Purpose |
|------|---------|
| fp-felles | Auth/OIDC, ABAC, JPA/DB, logging, REST clients, register integrations |
| fp-kontrakter | Shared DTOs/schemas (multilateral + bilateral) |
| fp-prosesstask | Async task framework (outbox/inbox/saga/scheduled) |
| fp-nare | Rule engine (specification pattern, traceable evaluation trees) |
| fp-tidsserie | Time-series of fom/tom/value segments |
| fp-graphql | Code-gen from GraphQL schemas |

### Tier 3 — Business rules (SemVer)

| Repo | Purpose |
|------|---------|
| fp-uttak | Leave allocation rules |
| fp-inngangsvilkar | Eligibility rules |
| fp-stonadskonto | Stønadskonto calculation |
| fp-ytelse-beregn | Benefit calculation rules |
| ft-beregning | Shared calculation (cross-team) |
| svp-uttak | Svangerskapspenger leave rules |

### Tier 4 — Frontend

| Repo | Role | Build |
|------|------|-------|
| foreldrepengesoknad | Citizen application form | Yarn + Turbo |
| fp-frontend | Case worker UI | pnpm + Turbo |
| fp-inntektsmelding-frontend | Employer income reporting | — |
| ft-frontend-saksbehandling | Shared case worker frontend | — |

### Tier 5 — Backend apps

| Repo | Role |
|------|------|
| fp-soknad | Citizen application backend |
| fp-mottak | Receive + validate incoming applications |
| fp-sak | Core case processing (behandling, aksjonspunkt, vedtak) |
| fp-abakus | Register data aggregation |
| fp-kalkulus | Benefit calculation service |
| fptilbake | Tilbakekreving |
| fp-los | Case queue (oppgavestyring) |
| fp-formidling | Document generation, correspondence |
| fp-dokgen | Document template engine |
| fpoppdrag | Payment order submission |
| fp-risk | Fraud risk scoring |
| fp-oversikt | Citizen case status view |
| fp-inntektsmelding | Employer income reporting backend |
| fp-inntektsmelding-api | ERP integration API |
| fp-tilgang | Access control service |

### Tier 6 — Testing

| Repo | Purpose |
|------|---------|
| fp-autotest | Integration + value-chain tests for all backend apps |
| vtp | Virtual Test Platform — mocks external services |

### Unicorns (outside standard stack)

| Repo | Stack | Note |
|------|-------|------|
| fp-ws-proxy | Spring Boot | SOAP/WS proxy to legacy |
| fp-infotrygd | Kotlin + Spring Boot | Legacy Infotrygd integration |
| fp-swagger | — | API documentation utility |
| fp-grunndata | — | Reference data |
| fp-jms-integrasjon | — | Legacy JMS messaging |

## Data flow

```
Citizen → foreldrepengesoknad → fp-mottak → fp-sak
                                              ├─ fp-abakus (register data)
                                              ├─ fp-inngangsvilkar (eligibility)
                                              ├─ fp-kalkulus (calc)
                                              └─ fp-uttak (allocation)
                                                  ↓
                                              Vedtak
                                                  ├─ fpoppdrag (payment)
                                                  └─ fp-formidling (letters)
Side: fp-los (queue), fp-risk (scoring), fptilbake (recovery), fp-oversikt (status view)
```

## Deployment

| Aspect | Tool |
|--------|------|
| Platform | NAIS (Kubernetes) |
| Environments | dev-gcp/dev-fss, prod-gcp/prod-fss |
| CI/CD | fp-gha-workflows |
| Image registry | Google Artifact Registry |
| Static analysis | SonarCloud (org: navikt) |
