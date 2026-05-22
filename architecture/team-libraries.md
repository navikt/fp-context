# Team Libraries — what to use from where

Authoritative for all Team Foreldrepenger backend repos. Defer to this when
choosing between a shared library component and a local implementation.

## fp-bom — dependency baseline

| Parent | Use in |
|--------|--------|
| `fp-parent-app` | Deployable applications |
| `fp-parent-lib` | Libraries |

Owns external dependency versions for the pure FP platform: Jakarta EE 11,
Jetty, Jersey (server only), Weld CDI, Hibernate, Jackson (moving from 2 to 3),
Kafka 4. Do not pin versions locally — defer to BOM. Upgrades land here first.

Not in fp-bom:
- JMS / IBM MQ — see `fp-jms-integrasjon`
- token-support — only used by unicorns (`fp-infotrygd`, `fp-ws-proxy`)
- No Spring Boot, no Ehcache

## fp-felles/felles — building blocks

Prefer these over local variants:

| Module | Purpose | Notes |
|--------|---------|-------|
| `feil` | Structured error codes + messages | |
| `log` | Logback/SLF4J structured logging | |
| `konfig` | App configuration | |
| `mapper` | Standard mapping helpers | |
| `kontekst` | Thread/request context | |
| `oidc` | Token validation (incoming) + provider/exchange (outgoing) | **Exception**: incoming maskinporten in fp-inntektsmelding-api |
| `abac`, `abac-kontekst` | XACML PEP/PDP framework | **Exception**: custom auth in fp-oversikt, fp-inntektsmelding(-api) |
| `db`, `auth-filter`, `server`, `klient`, `kafka-properties` | DB, web filters, Jetty bootstrap, REST helpers | |

## fp-felles/integrasjon — shared external clients

REST/GraphQL clients used by **more than one** app. Single-app integrations
stay in that app.

| Klient | System |
|--------|--------|
| `person-klient` | PDL |
| `saf-klient`, `safselvbetjening-klient` | SAF (arkiv) |
| `dokarkiv-klient` | Joark dokarkiv |
| `ereg-klient` | Enhetsregister |
| `oppgave-rest-klient` | Gosys oppgaver |
| `infotrygd-grunnlag-klient` | Infotrygd |
| `spokelse-klient` | Sykepenger-historikk |
| `tilgang-klient` | fp-tilgang |
| `rest-klient` | Base REST client |

## fp-prosesstask — async processing

Framework for persistent, fault-tolerant async work. Use for:

| Pattern | Use case |
|---------|----------|
| Outbox | Calls to REST/Kafka/MQ outside DB transaction; target must be idempotent |
| Inbox | Take ownership of inbound messages before further processing |
| Saga | Orchestrated multi-step transactions |
| Scheduled | Cron-like recurring jobs |

Properties: persistent, fail-fast, retryable after code/data fix.

## fp-tidsserie — period handling

`LocalDateSegment` (fom/tom + value) and `LocalDateTimeline` (set of
segments). Supports union, minus, disjoint and custom combiners. Auto-handles
knekkpunkter.

⚠️ **When values are aggregate quantities tied to interval duration**
(sums, totals, day counts) the combiner must recompute them after split or
merge — values are *not* automatically rescaled.

Not for statistical time-series analysis.

## fp-nare — Not-A-Rule-Engine

Specification-pattern / expression tree for business rules. Supports unary
(Sequence, Not, Node, Foreach), binary (And, Or), ternary (If/Else), and
n-ary (Conditional If/Or/Else) expressions.

Output (JSON) covers both rule specification and evaluation trace — usable
for legal/regulatory review and long-term storage. AsciiDoc rule docs via
Doclet.
