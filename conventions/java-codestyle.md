# Java Code Style

Java 25+. Prefer locally understandable code with explicit domain meaning. Use functional and fluent constructs when they improve that readability.

## Principles and Guidelines

- Write code for effective consumption by models and experienced developers. Linear readability over cleverness
- Prefer explicit types. Use inference-based constructs only when the type is obvious from the statement
- Code-sharing through delegation and composition, not inheritance
- Prefer records where appropriate, especially for DTOs and value types
- Embrace mechanisms from fp-felles (`DefaultJsonMapper`, `Environment`, `RestClient`, etc.) and fp-tidsserie
- Limit Jackson annotations to real need, e.g. avoid JsonProperty with same name as field, prefer JsonAlias for alternate names
- Prefer functional style over imperative style, especially for collection processing
- Prefer constructs for two-way navigation. e.g. AttributeConverter without autoApply for value types and enums
- Use validation / NotNull where applicable in entities and dtos
- Use builders for complex objects with many optional fields - enforce non-null for required fields
- Anti-corruption layer for consuming external systems: map responses to internal domain types and be aware of external change dynamics
- Separate contracts for external consumers from internal domain types. Avoid exposing internal types to external consumers
- Use fp-kontrakter for external contracts and multi-producer fp-internal contracts. Single producer fp-internal contracts in the producing repo
- Use `fp-parent-lib` for shared libraries and `fp-parent-app` for apps.
- Use multiple modules only for large or wide repos with distinct subdomains.

## Readability

- Prefer explicit types and preserve intentional type constraints
- Make collection shape and element types apparent. 
- Name dependencies by responsibility or domain
- Name values by meaning — not as compensation for missing types
- Keep declarations close to use and scopes small.
- Split complex pipelines at meaningful intermediate

## Functional style

### Optional over null

```java
// ✅
return behandling.getAksjonspunkter().stream()
    .filter(Aksjonspunkt::erÅpent)
    .findFirst()
    .map(Aksjonspunkt::getDefinisjon)
    .orElse(AksjonspunktDefinisjon.UDEFINERT);
```

### Stream over loops

```java
// ✅
ArbeidsgiverIdentifikator aktive = arbeidsforhold.stream()
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
Trekkdager total = perioder.stream().mapToInt(Periode::getTrekkdager).sum();
```

## Blueprints

### Value types

```java
public record Saksnummer(@JsonValue @NotNull String verdi) {
    public Saksnummer {
        Objects.requireNonNull(verdi, "saksnummer");
    }
    
    @Converter
    public class Converter implements AttributeConverter<Saksnummer, String> {

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
    
    @NotNull
    @NaturalId
    @Convert(converter = Saksnummer.Converter.class)
    @Column(name = "saksnummer", nullable = false)
    private Saksnummer saksnummer;
}
```

### Enums

```java
public enum YtelseType {

    ENGANGSTØNAD("ES"),
    FORELDREPENGER("FP"),
    SVANGERSKAPSPENGER("SVP")
    ;
    @JsonValue
    @EnumeratedValue
    private final String kode;

    YtelseType(String kode) {
        this.kode = kode;
    }

    private static final Map<String, YtelseType> KODE_MAP = Arrays.stream(values())
            .collect(Collectors.toMap(y -> y.kode, y -> y));

    public static YtelseType fraKode(String kode) {
        if (kode == null) return null;
        return KODE_MAP.get(kode);
    }
    
    @Converter
    public class Converter implements AttributeConverter<YtelseType, String> {

        @Override
        public String convertToDatabaseColumn(Saksnummer verdi) {
            return verdi == null ? null : verdi.verdi();
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
    
    @NotNull
    @Convert(converter = YtelseType.Converter.class)
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

- Don't comment what — make code readable
- Comment why for non-obvious decisions (legal requirements, workarounds)
- Javadoc on public APIs of shared libraries
