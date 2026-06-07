# Backend Authentication and Authorization

## Domains

- Case procesessing (saksbehandling) and administrative (forvaltning) endpoints: Full auth as below.
- Naiserator hook-endpoints (health, metrics under path "/internal"): no auth.
- Citizen-facing endpoints (`fp-soknad`, `fp-oversikt`): Standard authentication, custom authorization in endpoints
- Employer-facing endpoints (`fp-inntektsmelding`): Standard authentication, custom authorization in endpoints
- Employer-facing ERP endpoints (`fp-inntektsmelding-api`): Custom authentication and authorization
- Open services (only `fp-grunndata`): no auth.

## Authentication (inbound)

| Token type | Issuer | Typical consumer |
|---|---|---|---|
| Azure CC (client_credentials) | Azure AD | App-to-app (fp-sak → fp-abakus) |
| Azure OBO (on_behalf_of) | Azure AD | Frontend → backend (fp-frontend → fp-sak) |
| TokenX | TokenX | Citizen → backend (foreldrepengesoknad → fp-soknad) |

Deployed apps register RS Application that register `AuthenticationFilter` from `felles/server`, delegating validation to `felles/oidc`.
On successful validation, `AuthenticationFilter` populates `RequestKontekst` with identity and token info for downstream use (e.g. OBO/exchange) - see also `felles/kontekst`.

Exception: `fp-inntektsmelding-api` with local code for validating incoming Maskinporten tokens

## Authorization (per endpoint)

Every REST endpoint requires `@BeskyttetRessurs` unless from an excepted Domain.

| Parameter | Purpose                                        |
|---|------------------------------------------------|
| `actionType` | XACML action (READ, CREATE, UPDATE)            |
| `resourceType` | App-specific resource identifier               |
| `sporingslogg` | Enable/disable audit logging  |

All incoming request Dtos must implement `AbacDto` or use an `TilpassetAbacAttributt` to map reqest data to `AbacDataAttributter` for use by `felles/abac`.
Each app implements `PdpRequestBuilder` (defined in `felles/abac`) to map `AbacDataAttributter` to `AppRessursData` by mapping or looking up data.
Final access control decision is made by `BeskyttetRessursInterceptor` in `felles/abac`

Exceptions with no or custom access control:
- `fp-oversikt` implemented in endpoints
- `fp-inntektsmelding` AltInn roles and org number checks
- `fp-inntektsmelding-api` using AltInn roles and org number checks
- `fp-grunndata` and naiserator-hooks: no auth

## Adding a new endpoint

1. Annotate with `@BeskyttetRessurs(actionType=..., resourceType=...)`
2. Ensure `PdpRequestBuilder` handles the resource type
3. No explicit filter registration needed — `AuthenticationFilter` is global