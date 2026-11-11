# Java Code Style

Java 25+. Prefer locally understandable code with explicit domain meaning. Use functional and fluent constructs when they improve that readability.

## Principles and Guidelines

- Some of these are goals and does not reflect the current code-base - but new or touched code and changes should move the code-base in this direction.
- Write code for effective consumption by models and experienced developers. Linear readability over cleverness
- Prefer explicit types. Use inference-based constructs only when the type is obvious from the statement
- Prefer code-sharing through delegation and composition, not inheritance - unless enforcing policies (like in BaseEntitet)
- Prefer records where appropriate, especially for DTOs and value types
- Embrace mechanisms from `navikt/fp-felles` (`DefaultJsonMapper`, `Environment`, `RestClient`, etc.), `navikt/fp-prosesstask` and `navikt/fp-tidsserie`
- Use `DefaultJsonMapper` for all JSON (de)serialization, and assume the setup in that mapper. Don't create new JsonMappers or ObjectMappers.
- Limit Jackson annotations to real need, e.g. avoid JsonProperty with same name as field. Prefer JsonAlias for alternate names
- Don't suggest JsonCreator annotations for records or classes - the `DefaultJsonMapper` has fieldVisibility = ANY
- Prefer functional style over imperative style, especially for collection processing, but avoid sideeffects in streams
- Prefer constructs enabling two-way editor navigation - e.g. AttributeConverter without autoApply for value types and enums
- Use validation / NotNull where applicable in entities and dtos
- Use builders for complex objects with many optional fields - enforce non-null for required fields
- Anti-corruption layer for consuming external systems: map responses to internal domain types and be aware of external change dynamics
- Explicit absence semantics: Use Optional<T> for lookups where absence is expected; fail when a required value is missing
- Constructor injection: Prefer @Inject constructors; retain the no-arg constructor where CDI proxying requires it
- Use ApplicationScoped beans for long-lived and thread-safe services. Dependent beans for small-state services or when linked to ApplicationScoped beans. Keep existing RequestScoped beans but don't add more. Avoid SessionScope and ConversationScope.
- Requests and ProsessTasks run in a RequestContext, and so is the EntityManager. Kafka and MQ consumers need to ActivateRequestContext.
- Keep @Transactional on the outermost scope (REST-classes and those with ActivateRequestContext). Avoid nested transactions and @Transactional on inner service methods.

## Ongoing code transformation

- These should be done in targeted refactorings, not as part of feature work. Avoid large PRs with many unrelated changes.
- Align Hibernate annotations with actual database constraints (nullable, length, precision, scale, etc.) and add validation/NotNull where applicable. Avoid @Column(nullable = false) without @NotNull.
- Align frontend / backend naming and constraints (nullability or slightly different names (requiring a JsonProperty))
- Remove uncessary Jackson annotations - keep where they make a difference (JsonAlias or properties with different name).
- Remove Kodeverdi/UDEFINERT: Target required value or make nullable - beware of database constraints and existing data ("-").

## Readability

- Prefer explicit types and preserve intentional type constraints. Make collection shape and element types apparent. 
- Name dependencies by responsibility or domain. Name values by meaning — not as compensation for missing types
- Keep declarations close to use and scopes small.
- Split complex pipelines at meaningful intermediate steps

## Functional style

### Optional over null

```java
// ✅
return behandling.getAksjonspunkter().stream()
    .filter(Aksjonspunkt::erÅpent)
    .findFirst()
    .map(Aksjonspunkt::getDefinisjon);
```

### Stream over loops

```java
// ✅
List<ArbeidsgiverIdentifikator> aktive = arbeidsforhold.stream()
    .filter(Arbeidsforhold::erAktivt)
    .map(Arbeidsforhold::getArbeidsgiverIdentifikator)
    .toList();
```

### Method references where readable

```java
perioder.stream().filter(Periode::erGyldig).map(Periode::getFom).toList();
```

### Builders over setters

```java
// ✅
var periode = Stønadsperiode.builder()
    .medFom(start).medTom(slutt)
    .medDekningsgrad(Dekningsgrad.HUNDRE)
    .build();
```

### Avoid mutable locals

```java
// ✅
int total = perioder.stream().mapToInt(Periode::getTrekkdager).sum();
```

## Blueprints

### Value types

```java
public record Saksnummer(@JsonValue @NotNull String verdi) {
    public Saksnummer {
        Objects.requireNonNull(verdi, "saksnummer");
    }
    @Converter
    public static class SaksnummerConverter implements AttributeConverter<Saksnummer, String> {
        @Override
        public String convertToDatabaseColumn(Saksnummer verdi) {
            return verdi == null ? null : verdi.verdi();
        }
        @Override
        public Saksnummer convertToEntityAttribute(String verdi) {
            return verdi == null ? null : new Saksnummer(verdi);
        }
    }
}

@Entity(name = "Fagsak")
@Table(name = "FAGSAK")
public class Fagsak {
    // Id-attributes and other
    @NotNull
    @NaturalId
    @Convert(converter = Saksnummer.SaksnummerConverter.class)
    @Column(name = "saksnummer", nullable = false)
    private Saksnummer saksnummer;
}
```

### Enums

```java
public enum YtelseType {
    ENGANGSTØNAD("ES"),
    FORELDREPENGER("FP"),
    SVANGERSKAPSPENGER("SVP");
    @JsonValue
    // @EnumeratedValue for small repos and single-use in tables
    private final String kode;

    YtelseType(String kode) {
        this.kode = kode;
    }

    private static final Map<String, YtelseType> KODE_MAP = Arrays.stream(values())
            .collect(Collectors.toMap(y -> y.kode, y -> y));

    public static YtelseType fraKode(String kode) {
        return kode == null ? null : KODE_MAP.get(kode);
    }

    public String getKode() {
        return kode;
    }

    @Converter
    public static class YtelseTypeConverter implements AttributeConverter<YtelseType, String> {
        @Override
        public String convertToDatabaseColumn(YtelseType verdi) {
            return verdi == null ? null : verdi.getKode();
        }

        @Override
        public YtelseType convertToEntityAttribute(String verdi) {
            return verdi == null ? null : YtelseType.fraKode(verdi);
        }
    }
}

@Entity(name = "Fagsak")
@Table(name = "FAGSAK")
public class Fagsak {
    // Id-attributes and other
    @NotNull
    @Convert(converter = YtelseType.YtelseTypeConverter.class)
    @Column(name = "ytelse_type", nullable = false, updatable = false)
    private YtelseType ytelseType;
}
```

## Language features

| Feature | Use for |
|---------|---------|
| `record` | DTOs, value objects, immutable carriers |
| `sealed` interface/class | Restricted type hierarchies |
| Pattern matching | Switch over sealed types |
| Text blocks | Multi-line SQL, JSON, templates |
| `var` | Local variables with obvious types. Avoid for service/api calls. |

## Naming

| Element | Convention |
|---------|-----------|
| Domain code | Norwegian terms (Behandling, Aksjonspunkt, Vilkår) |
| Technical code | English (Repository, Service, Controller) |
| Classes | PascalCase |
| Methods/variables | camelCase |
| Constants | UPPER_SNAKE_CASE |
| Packages | lowercase, no underscores |

## Comments

- Don't comment the obvious — make code readable. May mention the overall role of a class or method, or the intent of a block of code.
- Be sober about your current context and avoid escalating clarifications into major points worth a comment. 
- Comment why for non-obvious decisions (legal requirements, workarounds)
- Javadoc on public APIs of shared libraries
