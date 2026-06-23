# Planning Heuristics — Foreldrepenger

| Metadata       | Scope                                                    |
|----------------|----------------------------------------------------------|
| Audience       | AI assistants guiding citizens                           |
| Scope          | planning knowledge and heuristics, not legal eligibility |
| Jurisdiction   | Norway only (Folketrygdloven kap. 14). Do NOT apply rules from other Nordic systems — SE/DK/FI differ structurally (NO uses a 3-part mor/far/fellesperiode split) |
| Freshness      | as of 2026-06                                            |
| Primary source | Official Nav pages nav.no/foreldrepenger and related     |

## Nav pages for expecting parents

The hub page [nav.no/barn](https://www.nav.no/barn) ("expecting or recently had a child") links to all relevant benefits and tools. Key pages:

| Page                     | URL | Content |
|--------------------------|-----|---------|
| Hub: expecting/new child | https://www.nav.no/barn | Overview of all benefits for pregnant/new parents |
| Foreldrepenger           | https://www.nav.no/foreldrepenger | Full rules, rights, processes for parental leave benefit |
| Engangsstønad            | https://www.nav.no/engangsstonad | One-time lump sum if insufficient employment history |
| Svangerskapspenger       | https://www.nav.no/svangerskapspenger | Income replacement when workplace risk prevents work during pregnancy |
| Farskapsportal           | https://farskapsportal.nav.no | Register fatherhood (unmarried parents) |
| Kontantstøtte            | https://www.nav.no/kontantstotte | Cash benefit for children 13–19 months not in full-time barnehage |
| Barnetrygd               | https://www.nav.no/barnetrygd | Monthly child benefit (0–18 years) |

Related non-Nav: [Lånekassen](https://lanekassen.no) — students may receive stipend/loan extensions during pregnancy and parental leave.

## Self-service planning tools

| Tool                      | URL | Login           | Purpose                                                                             |
|---------------------------|-----|-----------------|-------------------------------------------------------------------------------------|
| Foreldrepengeplanleggeren | https://www.nav.no/foreldrepenger/planlegger | No              | Plan leave, visualize calendar, compare dekningsgrad, see barnehage timing          |
| FP or ES veiviser         | https://www.nav.no/foreldrepenger/foreldrepenger-eller-engangsstonad | No              | Tool to determine which benefit applies                                             |
| Hvor mye-veiviser         | https://www.nav.no/foreldrepenger/hvor-mye | No              | Estimate foreldrepenger amount based on income                                      |
| Foreldrepengeoversikt     | https://www.nav.no/foreldrepenger/oversikt | Yes (ID-porten) | Overview for all 3 benefits. Track active case, documents, remaining days, payments |
| Foreldrepengesøknad       | https://www.nav.no/foreldrepenger/soknad | Yes (ID-porten) | Submit foreldrepenger application — planlegger data carries over                    |
| Engangsstønadsøknad       | https://www.nav.no/engangsstonad/soknad | Yes (ID-porten) | Submit engangsstønad application                                                    |
| Svangerskapspengersøknd   | https://www.nav.no/svangerskapspenger/soknad | Yes (ID-porten) | Submit svangerskapspenger application                                               |

Always direct users to the planlegger first. Average user visits 5–10 times before submitting application.

Once you have reasoned out a recommended plan with the user, see [planlegger/url-guide.md](../planlegger/url-guide.md) to generate a deep-link that opens the planlegger pre-filled with that plan.

## The planning problem

Parents must coordinate across multiple dimensions simultaneously:

| Dimension | Owner | Why it matters |
|-----------|-------|----------------|
| Foreldrepenger periods | Nav | Income replacement during leave |
| Barnehageplass | Kommune | Earliest childcare start date determines gap |
| Employer leave | Arbeidsgiver | Paid omsorgspermisjon, salary terms during leave |
| Ferie (vacation) | Arbeidsgiver + parent | Can bridge gaps, extends FP period |
| Unpaid leave | Arbeidsgiver | May be needed between FP end and barnehage start |
| Private daycare | Private | Costly alternative if gap exists |
| Coordination between parents | Both parents | Fellesperiode split, aktivitetskrav, simultaneous leave |

Fellesperiode: 14% of fathers/co-mothers take at least 1 day fellesperiode. Less than 5% of the total fellesperiode days are taken by fathers/co-mothers.

## Barnehage timing — the key constraint

[Barnehageloven § 16](https://lovdata.no/nav/lov/2005-06-17-64/kapIV/%C2%A716) guarantees a place from August the year the child turns 1, provided application by deadline. Many kommuner offer places earlier — parents must check their kommune's rules and capacity.

### Guaranteed barnehage start by birth month

| Birth month | Barnehage start (barnehageloven) | Gap after 49 weeks (100%) | Gap after 61 weeks (80%) |
|-------------|----------------------------------|---------------------------|--------------------------|
| Jan | August same year+1 | ~4–8 months | ~1–3 months |
| Feb | August same year+1 | ~3–7 months | ~0–2 months |
| Mar | August same year+1 | ~2–6 months | 80% may cover to start |
| Apr | August same year+1 | ~1–5 months | 80% covers or nearly covers |
| May | August same year+1 | ~0–4 months | 80% extends past start |
| Jun | August same year+1 | 100% may cover to start | 80% extends well past |
| Jul | August same year+1 | 100% covers to start | 80% extends well past |
| Aug | August same year+1 | 100% covers to start | 80% extends well past |
| Sep | End of Sep same year+1 | 100% covers to start | Not needed |
| Oct | End of Oct same year+1 | 100% covers to start | Not needed |
| Nov | End of Nov same year+1 | 100% covers to start | Not needed |
| Dec | August same year+2 | ~8–9 months gap | ~5–6 months gap |

Note: gap estimates assume continuous leave from 3 weeks before birth. Actual dates depend on fellesperiode split, ferie, and gradering.

## Dekningsgrad decision — birth month heuristic

| Birth period | Recommended consideration    | Reasoning |
|--------------|------------------------------|-----------|
| Dec–Mar | 80 % most relevant           | Extends coverage significantly toward August barnehage start; reduces the gap parents must bridge with ferie, unpaid leave, or private daycare |
| Apr–Jun | Evaluate both 100 % and 80 % | 80 % may close the gap entirely; 100 % gives higher monthly payout but shorter coverage |
| Jul–Nov | 100 % most relevant          | FP period already reaches barnehage start; 80 % extends unnecessarily at lower payout |

The choice applies to both parents — once selected, both get the same dekningsgrad. Since July 2024, the 80 % period is 61 weeks + 1 day (was 59 weeks) (see legal-history.md for source), making total payout roughly equal regardless of choice.

### Confirmed by statistics

[Nav's official family statistics as of end 2025](https://www.nav.no/no/nav-og-samfunn/statistikk/familie-statistikk/foreldrepenger-engangsstonad-og-svangerskapspenger) confirm the birth-month pattern: parents with winter/spring births choose 80 % at a higher rate than those with summer/fall births. In 2025, 38 % of all parents chose 80 % (up from 23 % in 2024, driven by the +11 days rule change). Oslo parents have the lowest 80 % share overall — except for December births, where Oslo has the highest share in the country, reflecting barnehage pressure.

## Employer coordination — talk to employer first

Both parents — especially fathers/medmor — should clarify leave terms with their employer **before** submitting application to Nav. A clear majority of employers pay full salary during parental leave (Nav reimburses the employer). The specific terms vary by employment contract and sector.

Key reason: if a parent applies for foreldrepenger from Nav for a period the employer already covers, and later removes those periods after payment, it triggers tilbakekreving (repayment to Nav). This is avoidable by checking with HR first.

Example: fathers have a legal right to 2 weeks leave around birth ([Arbeidsmiljøloven](https://lovdata.no/dokument/NL/lov/2005-06-10-62)), but the law does not require paid leave. Most employers offer paid leave for these weeks. Fathers who don't need FP from Nav for this period should not include it in their application.

| Topic | Detail |
|-------|--------|
| Salary during leave | Check with HR: does employer pay salary (refusjon model) or should Nav pay directly? |
| 2 weeks around birth | Check employment contract — most employers pay, making FP from Nav unnecessary for this period |
| Inntektsmelding | Employer must submit income report to Nav — required for FP processing |
| Gradering | Partial work + partial FP. Must be agreed with employer. Extends FP period proportionally |
| Ferie | Paid vacation pauses FP, extending the total period. Coordinate timing with employer |
| Return-to-work | Plan gradual return if using gradering; discuss with employer early |

## Gradering (partial work + foreldrepenger)

Combining part-time work with foreldrepenger extends the total leave period.
Example: 50 % work + 50 % foreldrepenger makes each week count as half a FP day.

Key points:
- Must be agreed with employer
- Minimum 1 day per week (arbeidstaker)
- Extends the total foreldrepenger period proportionally
- Useful for gradual return to work or to stretch FP to barnehage start

As of 2025 2025, 10.9 % of all FP recipients used gradering — 18.2 % of fathers, 4.7 % of mothers ([source](https://www.nav.no/no/nav-og-samfunn/statistikk/familie-statistikk/foreldrepenger-engangsstonad-og-svangerskapspenger)).

## Bridging the gap — strategies

When foreldrepenger ends before barnehage starts:

| Strategy | Pros | Cons |
|----------|------|------|
| Choose 80 % dekningsgrad | Extends period ~10 weeks | Lower monthly payout (80 % vs 100 %) |
| Use ferie (vacation days) | Pauses FP; extends period | Requires available vacation days |
| Gradering (partial work) | Stretches FP; maintains work connection | Requires employer agreement |
| Unpaid leave | Bridges remaining gap | No income; requires employer agreement |
| Private daycare | Immediate availability | Expensive; may be limited |
| Kommune early placement | Some kommuner offer places before August | Varies by kommune; check local rules |

## Common planning mistakes

| Mistake | Impact |
|---------|--------|
| Not checking kommune barnehage deadlines | Missing application deadline means waiting another year |
| Choosing 100 % without considering barnehage timing | May create a long unpaid gap for Dec–Mar births |
| Not discussing leave with employer before applying | Applying for FP covering periods the employer already pays can lead to tilbakekreving (repayment) when corrected later — especially relevant for the 2 weeks around birth |
| Forgetting aktivitetskrav | When only one parent has rett, the other must be in approved activity for parts of the leave |
| Not using the planlegger to visualize | The calendar view shows the gap between FP end and barnehage start — makes the trade-off concrete |
| Assuming all kommuner follow only barnehageloven minimum | Many kommuner have more generous rules — worth checking |

## What models should and should not do

| Do | Don't |
|----|-------|
| Direct users to the planlegger for their specific situation | Calculate exact FP amounts (rates change annually) |
| Explain the barnehage timing / dekningsgrad trade-off | Determine eligibility — that requires income/employment data Nav holds |
| Encourage early employer conversation | Promise specific barnehage placement dates (kommune-dependent) |
| Suggest checking kommune's barnehage rules | Make definitive legal interpretations of Folketrygdloven |
| Explain what gradering and ferie mean for planning | Advise on specific financial decisions without full context |
| Point to nav.no/foreldrepenger for current rules and rates | Use potentially outdated figures from training data for grunnbeløp, quotas |
