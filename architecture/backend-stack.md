# Backend Stack

## Core stack

| Layer | Technology | Source of patterns |
|-------|-----------|-------------------|
| Server | Jetty (embedded) | fp-felles |
| REST | Jersey (JAX-RS) | fp-felles |
| CDI | Weld | fp-felles |
| ORM | Hibernate (JPA + Validation) | fp-felles |
| Serialization | Jackson (migrating to v3) | fp-felles |
| Async | fp-prosesstask | fp-prosesstask |
| Auth | OIDC (Azure AD, TokenX) | fp-felles |
| Access control | ABAC via `@BeskyttetRessurs` | fp-felles |
| DB migration | Flyway | `src/main/resources/db/migration/` |
| Build | Maven | fp-bom (parent POM) |
| Java | 25+ | fp-bom |

## Patterns

| Concern | Pattern |
|---------|---------|
| REST endpoints | Jersey resource + DTO + `@BeskyttetRessurs` on every endpoint |
| Database | JPA entity + base class from fp-felles + Flyway migration |
| Async work | `ProsessTaskHandler` — never raw threads/executors |
| External REST | Java HttpClient configured via fp-felles |
| GraphQL clients | Generated via fp-graphql from schema |
| Contracts | Defined in fp-kontrakter, versioned |
| Business rules | fp-nare specification pattern in a SemVer library repo |

## Unicorn stack — do not use as pattern

| Repo | Stack | Reason |
|------|-------|--------|
| fp-ws-proxy | Spring Boot | SOAP/WS proxy to legacy |
| fp-infotrygd | Kotlin + Spring Boot | Legacy integration |
