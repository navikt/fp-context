# Frontend Stack

## Core stack

| Technology | Usage |
|-----------|-------|
| React | UI framework |
| Aksel (`@navikt/ds-*`) | Nav design system — components, tokens, icons |
| React Hook Form | Forms + validation |
| TanStack Query | Server state, data fetching, caching |
| TypeScript | Strict mode |

## Repos

| Repo | Audience | Package manager | Build |
|------|----------|----------------|-------|
| foreldrepengesoknad | Citizen | Yarn workspaces | Turbo |
| fp-frontend | Case worker | pnpm workspaces | Turbo |
| fp-inntektsmelding-frontend | Employer | — | — |
| ft-frontend-saksbehandling | Shared (case worker) | — | — |

## Rules

- Use Aksel components — do not build custom when an Aksel component exists
- WCAG 2.1 AA compliance on all citizen-facing flows
- React Hook Form for all forms
- TanStack Query for all API communication
- TypeScript strict mode

## Docs

- [Aksel](https://aksel.nav.no/)
- [TanStack Query](https://tanstack.com/query/latest)
- [React Hook Form](https://react-hook-form.com/)
