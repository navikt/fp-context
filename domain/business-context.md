# Business Context — Foreldrepenger

## Legal basis

| Source                                                                                   | Role |
|------------------------------------------------------------------------------------------|------|
| [Folketrygdloven kap. 14](https://lovdata.no/dokument/NL/lov/1997-02-28-19/KAPITTEL_5-9) | Primary law governing parental benefits |
| [Rundskriv til kap. 14](https://lovdata.no/nav/rundskriv/r14-00)                         | Interpretive guidelines, established practice |
| Statens Økonomireglement                                                                 | Payment ordering rules |
| EU family directives                                                                     | Cross-border coordination |
| WCAG 2.1 AA                                                                              | Accessibility for citizen-facing services |
| GDPR + Nav internal policy                                                               | Privacy, audit, logging |

## Benefits

| Code | Norwegian | Description | Citizen info |
|------|-----------|-------------|--------------|
| FP | Foreldrepenger | Income-replacement for parents of newborn/adopted children; complex quota rules | https://www.nav.no/foreldrepenger |
| ES | Engangsstønad | One-time lump sum for parents without sufficient employment history | https://www.nav.no/engangsstonad |
| SVP | Svangerskapspenger | Benefit for pregnant women unable to work due to workplace risk | https://www.nav.no/svangerskapspenger |

## Key external data sources

| Source | Role | Reference |
|--------|------|-----------|
| Inntektsmelding | Employer confirms income, claims refusjon for salary paid during leave, reports special cases (permisjon, sluttdato, naturalytelser) | https://www.nav.no/arbeidsgiver/inntektsmelding |
| A-ordningen | Monthly employment+income reporting from employers to Skatteetaten/Nav. Primary source for evaluating rights to FP and for calculating FP/SVP | https://www.skatteetaten.no/bedrift-og-organisasjon/arbeidsgiver/a-meldingen/om-a-ordningen/om-a-ordningen/ |

A-ordningen data is consumed via fp-abakus (registerdata). Inntektsmelding flows
through fp-inntektsmelding(-api) into fp-mottak → fp-sak.

## Value chain

```
Citizen/Employer → Mottak → Saksbehandling → Vedtak → Utbetaling
                                                    → Brev
```

| Stage | Repos |
|-------|-------|
| Søknad (apply) | foreldrepengesoknad, fp-soknad |
| Inntektsmelding (employer) | fp-inntektsmelding(-frontend, -api) |
| Mottak (receive) | fp-mottak |
| Behandling (case) | fp-sak |
| Registerdata | fp-abakus |
| Beregning (calc) | fp-kalkulus, ft-beregning |
| Uttak (allocation) | fp-uttak, svp-uttak, fp-stonadskonto |
| Vilkår (eligibility) | fp-inngangsvilkar |
| Oppgaver (queue) | fp-los |
| Vedtak (decision) | fp-sak |
| Utbetaling (payment) | fp-sak sends oppdrag to OS (external) over JMS |
| Simulering (balance impact) | fpoppdrag (simulates plan changes, provides repayment periods) |
| Brev (letters) | fp-formidling, fp-dokgen |
| Tilbakekreving (recovery) | fptilbake (kravgrunnlag from OS over JMS) |
| Risk scoring | fp-risk |
| Citizen overview | fp-oversikt |

## Core concepts

| Term | Description                                                                                                                                                                                                                                                                           |
|------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Familiehendelse | Birth or adoption event — anchors one fagsak per ytelse per parent. Determines the leave window (typically up to 3 years from start)                                                                                                                                                  |
| Fagsak | All parts of one parental benefit for one familiehendelse: applications, client/employer interactions, behandlinger, vedtak, documents, payments. Essentially a folder grouping everything tied to that event                                                                         |
| Saksnummer | Stable fagsak identifier — used as reference in Nav's central archive (Joark/SAF) and toward OS                                                                                                                                                                                       |
| Behandling | A series of steps triggered by an application or event (e.g. birth, klage, other parent's uttak overlap, other benefits) and ending in a vedtak (grant/deny/change) or dismissal. Types: førstegang, revurdering, klage, anke, innsyn; tilbakekreving runs as behandling in fptilbake |
| Behandlingssteg | Sequential step in case processing                                                                                                                                                                                                                                                    |
| Aksjonspunkt | Pause requiring manual or automated resolution                                                                                                                                                                                                                                        |
| Autopunkt | Aksjonspunkt resolved automatically                                                                                                                                                                                                                                                   |
| Vilkår | Legal eligibility condition                                                                                                                                                                                                                                                           |
| Beregningsgrunnlag | Income basis for benefit amount                                                                                                                                                                                                                                                       |
| Uttak | Day allocation across periods and parents                                                                                                                                                                                                                                             |
| Stønadskonto | Day quota per parent (mødrekvote, fedrekvote, fellesperiode)                                                                                                                                                                                                                          |
| Vedtak | Formal decision to grant, change or deny a benefit. Materialized as: payment plan (oppdrag) to OS, vedtaksbrev to client, and events notifying other Nav systems                                                                                                                      |
| Oppdrag | Payment instruction (order/orderline) sent from fp-sak to OS over JMS                                                                                                                                                                                                                 |
| OS (Oppdragssystemet) | External payment system — owns client balance and employer refunds; not part of team portfolio                                                                                                                                                                                        |
| Kvittering | Receipt from OS on return JMS queue                                                                                                                                                                                                                                                   |
| Kravgrunnlag | Repayment claim from OS to fptilbake over JMS — source of truth for tilbakekreving                                                                                                                                                                                                    |
| Aktør | Person identified by aktør-ID (unique ID - can cover history of fødselsnummers)                                                                                                                                                                                                       |

## Uttak (leave allocation)

Most complex business area. Implemented in fp-uttak using fp-nare engine.

| Concept | Description                                    |
|---------|------------------------------------------------|
| Mødrekvote | Mother's reserved quota                        |
| Fedrekvote | Father's reserved quota                        |
| Fellesperiode | Shared period                                  |
| Foreldrepenger før fødsel | Up to 3 weeks before birth - only for mother   |
| Samtidig uttak | Both parents on leave simultaneously           |
| Flerbarnsdager | Extra days for multiple births                 |
| Minsterett | Minimum entitlement regardless of other parent |
| Dekningsgrad | Coverage rate: 80% or 100%                     |
| Gradering | Partial work + partial benefit                 |
| Utsettelse | Postponement of period                         |
| Overføring | Transfer of quota between parents              |
