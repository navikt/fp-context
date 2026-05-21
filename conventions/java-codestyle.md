# Java Code Style

Java 25+. Functional, fluent, immutable-by-default.

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
var aktive = arbeidsforhold.stream()
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
var total = perioder.stream().mapToInt(Periode::getTrekkdager).sum();
```

## Language features

| Feature | Use for |
|---------|---------|
| `record` | DTOs, value objects, immutable carriers |
| `sealed` interface/class | Restricted type hierarchies |
| Pattern matching | Switch over sealed types |
| Text blocks | Multi-line SQL, JSON, templates |
| `var` | Local variables with obvious types |

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
