# Frontend Technology Stack

## Core Stack

| Technology          | Usage                                                           |
| ------------------- | --------------------------------------------------------------- |
| **React**           | UI framework                                                    |
| **Aksel**           | Nav's design system (@navikt/ds-\*) — components, tokens, icons |
| **React Hook Form** | Form management and validation                                  |
| **TanStack Query**  | Server state management, data fetching, caching                 |
| **TypeScript**      | Type safety throughout                                          |

## Repositories

### foreldrepengesoknad (citizen-facing)

- **Package manager**: pnpm
- **Monorepo**: Workspaces with apps/ and packages/
- **Build**: Turbo
- **Server**: Custom Node.js server (server/, server-uinnlogget/)
- **Storybook**: Component documentation

### fp-frontend (case worker UI)

- **Package manager**: pnpm
- **Monorepo**: pnpm workspaces with apps/ and packages/
- **Build**: Turbo (turbo.json)
- **Server**: Custom Node.js server
- Additional shared components in repository ft-frontend-saksbehandling

### fp-inntektsmelding-frontend (employer-facing)

- Employer-side income reporting interface

## Conventions

- Use Aksel components as the primary building blocks — don't build custom
  components when an Aksel component exists
- Follow Nav's accessibility (WCAG) requirements in all user-facing components
- Use React Hook Form for all form handling
- Use TanStack Query for all API communication
- TypeScript strict mode

## Docs

- [Aksel](https://aksel.nav.no/)
