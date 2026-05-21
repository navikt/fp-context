# Testing

## Test layers

| Layer | Location | Framework |
|-------|----------|-----------|
| Unit | `src/test/java/` per repo | JUnit 6, AssertJ, Mockito |
| Integration | fp-autotest | JUnit 5 + @Tag + Maven profiles |
| Virtualization | vtp | Mocks external services |

## Integration test suites (in fp-autotest)

| Suite | Tags | Covers |
|-------|------|--------|
| fpsak | fpsak | Core case processing |
| fptilbake | fptilbake, tilbakekreving | Recovery |
| fpkalkulus | fpkalkulus | Calculation service |
| fplos | fplos | Case queue |
| verdikjede | verdikjede | End-to-end value chain |

Sub-suites by ytelse: `foreldrepenger`, `engangsstonad`, `svangerskapspenger`.

## App → suite mapping

| App | Suites |
|-----|--------|
| fp-sak | fpsak, verdikjede |
| fp-abakus | fpsak, verdikjede |
| fp-kalkulus | fpkalkulus, fpsak, verdikjede |
| fptilbake | fptilbake, verdikjede |
| fplos | fplos |
| fp-formidling, fpoppdrag, fp-mottak, fp-risk, fp-oversikt, fp-dokgen, fp-inntektsmelding, fp-soknad | verdikjede |

## Run commands (in fp-autotest)

```bash
mvn test -P <suite>                              # full suite
mvn test -P <suite> -Dtest=<ClassName>           # specific class
mvn test -P <suite> -Dtest="<Class>#<method>"    # single method
```

## Unit test conventions

- `@DisplayName` (Norwegian for domain tests)
- AssertJ fluent assertions: `assertThat(x).isEqualTo(y)`
- Mockito with constructor injection over field injection
- Records or builders for test data
