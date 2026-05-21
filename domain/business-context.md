# Business Context — Foreldrepenger

## Legal basis

| Source | Role |
|--------|------|
| [Folketrygdloven kap. 14](https://lovdata.no/dokument/NL/lov/1997-02-28-19/KAPITTEL_5-9) | Primary law governing parental benefits |
| Rundskriv til kap. 14 | Interpretive guidelines, established practice |
| Statens Økonomireglement | Payment ordering rules |
| EU family directives | Cross-border coordination |
| WCAG 2.1 AA | Accessibility for citizen-facing services |
| GDPR + Nav internal policy | Privacy, audit, logging |

## Benefits

| Code | Norwegian | Description |
|------|-----------|-------------|
| FP | Foreldrepenger | Income-replacement for parents of newborn/adopted children; complex quota rules |
| ES | Engangsstønad | One-time lump sum for parents without sufficient employment history |
| SVP | Svangerskapspenger | Benefit for pregnant women unable to work due to workplace risk |

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
| Utbetaling (payment) | fpoppdrag |
| Brev (letters) | fp-formidling, fp-dokgen |
| Tilbakekreving (recovery) | fptilbake |
| Risk scoring | fp-risk |
| Citizen overview | fp-oversikt |

## Core concepts

| Term | Description |
|------|-------------|
| Fagsak | Top-level case container per citizen+ytelse |
| Behandling | A processing instance (førstegangs, revurdering, klage, anke, innsyn) |
| Behandlingssteg | Sequential step in case processing |
| Aksjonspunkt | Pause requiring manual or automated resolution |
| Autopunkt | Aksjonspunkt resolved automatically |
| Vilkår | Legal eligibility condition |
| Beregningsgrunnlag | Income basis for benefit amount |
| Uttak | Day allocation across periods and parents |
| Stønadskonto | Day quota per parent (mødrekvote, fedrekvote, fellesperiode) |
| Vedtak | Formal decision on a behandling |
| Oppdrag | Payment instruction to utbetaling system |
| Aktør | Person identified by aktør-ID (pseudonym for fødselsnummer) |

## Uttak (leave allocation)

Most complex business area. Implemented in fp-uttak using fp-nare engine.

| Concept | Description |
|---------|-------------|
| Mødrekvote | Mother's reserved quota |
| Fedrekvote | Father's reserved quota |
| Fellesperiode | Shared period |
| Foreldrepenger før fødsel | Up to 3 weeks before birth |
| Samtidig uttak | Both parents on leave simultaneously |
| Flerbarnsdager | Extra days for multiple births |
| Minsterett | Minimum entitlement regardless of other parent |
| Dekningsgrad | Coverage rate: 80% or 100% |
| Gradering | Partial work + partial benefit |
| Utsettelse | Postponement of period |
| Overføring | Transfer of quota between parents |
