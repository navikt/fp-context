# Glossary — Norwegian Domain Terms

## People and roles

| Term            | English        | Context                          |
|-----------------|----------------|----------------------------------|
| Bruker / Søker  | Citizen        | The person applying for benefint |
| Annen forelder  | Co-parent      | Relevant for quota sharing       |
| Saksbehandler   | Case worker    | Nav employee processing cases    |
| Beslutter       | Approver       | Fatter vedtak                    |
| Arbeidsgiver    | Employer       | Submits inntektsmelding          |
| Verge           | Legal guardian | Acts on behalf of applicant      |

## Externals (in scope here)

| External                           | Covers                                               |
|------------------------------------|------------------------------------------------------|
| Joark (document journal + archive) | saf (read) + dokarkiv (write) + dokdist (distribute) |
| OS (Oppdragssystemet)              | Payment system; owns client and employer balance     |
| PDL (persondataløsningen)          | Folkeregisteret and Nav-local identities             |

## General concepts 

| Entity                | Description                                               | Code home                                     |
|-----------------------|-----------------------------------------------------------|-----------------------------------------------|
| Søknad                | Application from citizen                                  | foreldrepengesoknad, fp-soknad                |
| Fagsak or Sak         | Main case concept - full "folder" for one Familiehendelse | fp-sak                                        |
| Saksnummer            | Stable case ID, used throughout value-chain               | fp-sak                                        |
| Behandling            | One processing instance ending in vedtak/dismissal        | fp-sak (fptilbake)                            |
| Forespørsel           | Request to employer for inntektsmelding                   | fp-inntektsmelding(-frontend)                 |
| Inntektsmelding or IM | Employer's income report and reimbursement claim          | fp-inntektsmelding, fp-sak, fp-abakus         |
| Vedtak                | Decision: grant/change/deny                               | fp-sak, fptilbake                             |
| Vedtaksbrev           | Decision letter                                           | fp-sak + fp-formidling + fp-dokgen, fptilbake |
| Oppdrag               | Payment instruction to OS                                 | fp-sak                                        |
| Simulering            | Simulated balance impact for planned changes              | fpoppdrag                                     |
| Kravgrunnlag          | Repayment claim from OS                                   | fptilbake                                     |
| Klage                 | Complaint on vedtak from citizen                          | fp-sak                                        |
| Anke                  | Second appeal (Trygderetten)                              | fp-sak                                        |
| Personhendelse        | Event from Folkeregisteret via PDL                        | fp-mottak, fp-sak                             |
| Journalhendelse       | Event from archive / Joark                                | fp-mottak, fp-sak                             |
| Saksoversikt          | Citizen's view of their case(s)                           | fp-oversikt                                   |

## Case processing

| Term                      | English                                                                          | Code home  |
|---------------------------|----------------------------------------------------------------------------------|------------|
| Behandlingsgrunnlag       | Data sets used by behandling - structure: application, register, saksbehandler   | fp-sak     |
| Familiehendelse           | Family event (birth/adoption) — anchor for fagsak                                | fp-sak     |
| IAY-grunnlag              | Inntekt - arbeid - ytelser                                                       | fp-abakus |
| Førstegangsbehandling     | Initial søknad processing                                                        | fp-sak     |
| Revurdering(sbehandling)  | Reassessment (event-driven changes)                                              | fp-sak     |
| Tilbakekrevingsbehandling | Recovery of overpaid benefit                                                     | fptilbake  |
| Klagebehandling           | Processing of complaint on vedtak                                                | fp-sak     |
| Innsynsbehandling         | Document access request                                                          | fp-sak     |
| Ytelsevedtak              | Grant/deny/change decision ES/FP/SVP — materialized as oppdrag + letter + events | fp-sak     |
| Tilbakekrevingsvedtak     | Decision on repayment — materialized as OS-messge + letter + events              | fptilbake  |
| Klagevedtak               | Decision on klage - materialized as letter + oppgave                             | fp-sak     |

## Benefit specifics

| Term | English | Code home |
|------|---------|-----------|
| Ytelse | Benefit | fp-sak, fp-kontrakter |
| Dekningsgrad | Coverage rate (80/100%) | fp-uttak |
| Stønadsperiode | Benefit period | fp-uttak |
| Stønadskonto | Day quota account | fp-stonadskonto |
| Trekkdager | Withdrawn days | fp-uttak |
| Utsettelse | Postponement | fp-uttak |
| Overføring | Quota transfer | fp-uttak |
| Gradering | Partial work + partial benefit | fp-uttak |
| Aktivitetskrav | Other parent activity requirement | fp-uttak |

## Income and calculation

| Term | English | Code home |
|------|---------|-----------|
| Inntektsmelding | Income report from employer | fp-inntektsmelding |
| Beregningsgrunnlag | Calculation basis | fp-kalkulus |
| Inntektskategori | Income category (arbeidstaker, frilanser, selvstendig) | fp-kalkulus |
| Refusjon | Employer reimbursement | fp-kalkulus, fpoppdrag |
| Dagsats | Daily benefit rate | fp-kalkulus |
| Grunnbeløp (G) | Nav base amount, annually adjusted | fp-kalkulus |

## System / technical

| Term | English | Code home |
|------|---------|-----------|
| Prosesstask | Async task | fp-prosesstask |
| Aksjonspunkt | Action point (pause) | fp-sak |
| Autopunkt | Auto-resolved aksjonspunkt | fp-sak |
| Behandlingssteg | Processing step | fp-sak |
| Registeropplysninger | External register data | fp-abakus |
| Vilkårsvurdering | Eligibility assessment | fp-inngangsvilkar |
| Uttaksplan | Allocated leave plan | fp-uttak |
| Simulering | Payment preview pre-vedtak | fp-sak, fpoppdrag |
