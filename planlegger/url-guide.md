# Guide: Generere en direkte lenke til foreldrepengeplanleggeren

Denne guiden er beregnet på AI-assistenter (f.eks. GitHub Copilot, ChatGPT, Claude) som vil
hjelpe en bruker med å generere en ferdig lenke til **Foreldrepengeplanleggeren** på nav.no.
Lenken åpner planleggeren direkte på planoversikts-steget med et foreslått permisjonsopplegg
som brukeren kan tilpasse videre.

---

## Oversikt

Planleggeren leser tilstandsdata fra en `data`-query-parameter i URL-en. Verdien er en
lz-komprimert (lz-string `compressToEncodedURIComponent`) JSON-streng.

> ⚠️ **Du MÅ kjøre kode for å lage `data`-verdien.** lz-string-komprimering kan ikke
> regnes ut «i hodet» av en språkmodell. Bruk kodesnutten i Steg 3 (JS eller Python) til å
> produsere strengen. **Aldri** skriv eller gjett den komprimerte strengen manuelt — da blir
> URL-en korrupt. Har du ikke kjøre-kode-verktøy tilgjengelig: forklar brukeren stegene i
> stedet for å oppgi en oppdiktet lenke.

**Basis-URL:**
```
https://www.nav.no/foreldrepenger/planlegger/planen-deres?data=<komprimert-json>
```

---

## Steg 1: Samle informasjon fra brukeren

Før du kan generere lenken **må** du spørre brukeren om følgende informasjon. Still spørsmålene
ett om gangen dersom du ikke allerede har svaret.

### Obligatoriske spørsmål

1. **Hvem planlegger?**
   Velg én av følgende:
   - Mor og far
   - Mor og medmor
   - Far og far *(kun ved adopsjon)*
   - Bare mor (aleneomsorg)
   - Bare far (aleneomsorg)

2. **Navn** *(valgfritt, men gjør planen mer personlig)*
   - Navn på søker 1 (mor, eller far 1)
   - Navn på søker 2 (far, medmor, eller medfar) — ikke relevant for alene-scenariene

3. **Er barnet født, ikke født ennå (termin), eller adoptert?**
   - Hvis **født**: hva er fødselsdatoen? (format: ÅÅÅÅ-MM-DD)
   - Hvis **termin**: hva er termindatoen? (format: ÅÅÅÅ-MM-DD)
   - Hvis **adopsjon**: hva er overtakelsesdatoen og barnets fødselsdato?

4. **Antall barn?** (1, 2, eller 3+)

5. **Arbeidssituasjon for søker 1 (mor/far)?**
   Velg én:
   - Jobber / er selvstendig næringsdrivende / frilanser
   - Har uføretrygd
   - Jobber ikke / ingen inntekt

6. **Jobber / er søker 2 (den andre forelderen) yrkesaktiv?**
   Svar ja/nei. Spørsmålet er bare relevant dersom det er to søkere.

7. **100 % eller 80 % dekningsgrad?**
   - 100 % utbetaling over kortere periode (anbefalt standardvalg)
   - 80 % utbetaling over lengre periode

8. **Fordeling av fellesperioden** *(bare relevant når begge jobber og det ikke er far og far)*
   Spør om søker 1 ønsker noe av fellesperioden, eller om de ønsker å sette dette til 0
   (planen fordeler da alt til standard). Oppgi i antall uker søker 1 ønsker (0 er gyldig).

---

## Steg 2: Bygg JSON-objektet

Sett sammen et JSON-objekt med nøklene beskrevet nedenfor. Ta bare med de nøklene som er
relevante for scenariet.

### `HVEM_PLANLEGGER`

```json
// Mor og far
{ "type": "morOgFar", "navnPåMor": "Kari", "navnPåFar": "Ola" }

// Mor og medmor
{ "type": "morOgMedmor", "navnPåMor": "Kari", "navnPåMedmor": "Lise" }

// Far og far (kun adopsjon)
{ "type": "farOgFar", "navnPåFar": "Per", "navnPåMedfar": "Kim" }

// Bare mor (aleneomsorg)
{ "type": "mor" }

// Bare far (aleneomsorg)
{ "type": "far" }
```

### `OM_BARNET`

```json
// Barn ikke født ennå (termin)
{
  "erFødsel": true,
  "antallBarn": "1",
  "erBarnetFødt": false,
  "termindato": "2025-09-01"
}

// Barn allerede født
{
  "erFødsel": true,
  "antallBarn": "1",
  "erBarnetFødt": true,
  "fødselsdato": "2025-03-15",
  "termindato": "2025-03-20"   // termindato er valgfritt for fødte barn
}

// Adopsjon
{
  "erFødsel": false,
  "antallBarn": "1",
  "overtakelsesdato": "2025-06-01",
  "fødselsdato": "2024-01-15"
}
```

`antallBarn` er alltid en streng: `"1"`, `"2"` eller `"3"`.

### `ARBEIDSSITUASJON`

```json
// Begge jobber (delt uttak)
{ "status": "jobber", "jobberAnnenPart": true }

// Kun søker 1 jobber
{ "status": "jobber", "jobberAnnenPart": false }

// Søker 1 har uføretrygd, søker 2 jobber
{ "status": "ufør", "jobberAnnenPart": true }

// Søker 1 har uføretrygd, søker 2 jobber ikke
{ "status": "ufør", "jobberAnnenPart": false }

// Ingen jobber
{ "status": "jobberIkke", "jobberAnnenPart": false }

// Kun søker 2 jobber
{ "status": "jobberIkke", "jobberAnnenPart": true }
```

Gyldige verdier for `status`: `"jobber"` | `"ufør"` | `"jobberIkke"`

### `HVOR_LANG_PERIODE`

```json
// 100 % utbetaling (kortere periode)
{ "dekningsgrad": "100" }

// 80 % utbetaling (lengre periode)
{ "dekningsgrad": "80" }
```

### `FORDELING` *(kun når begge jobber og det ikke er far og far ved fødsel)*

```json
// Søker 1 tar ingen av fellesperioden (all til søker 2 / standard)
{ "antallDagerSøker1": 0 }

// Søker 1 tar 10 uker (= 50 dager) av fellesperioden
{ "antallDagerSøker1": 50 }
```

`antallDagerSøker1` angis i **uttaksdager** (5 dager = 1 uke).

---

## Steg 3: Komprimer og bygg URL-en

JSON-objektet må komprimeres med `lz-string` sin `compressToEncodedURIComponent`-funksjon
før det legges på URL-en. Output er direkte URL-trygt og skal ikke `encodeURIComponent`-es
en gang til.

### Eksempel i JavaScript / Node.js

```js
import { compressToEncodedURIComponent } from 'lz-string';

const data = {
  HVEM_PLANLEGGER: { type: 'morOgFar', navnPåMor: 'Kari', navnPåFar: 'Ola' },
  OM_BARNET: { erFødsel: true, antallBarn: '1', erBarnetFødt: false, termindato: '2025-09-01' },
  ARBEIDSSITUASJON: { status: 'jobber', jobberAnnenPart: true },
  HVOR_LANG_PERIODE: { dekningsgrad: '100' },
  FORDELING: { antallDagerSøker1: 0 },
};

const url =
  'https://www.nav.no/foreldrepenger/planlegger/planen-deres?data=' +
  compressToEncodedURIComponent(JSON.stringify(data));
```

### Eksempel i Python

```python
import json
import lzstring  # pip install lzstring

data = {
    "HVEM_PLANLEGGER": {"type": "morOgFar", "navnPåMor": "Kari", "navnPåFar": "Ola"},
    "OM_BARNET": {"erFødsel": True, "antallBarn": "1", "erBarnetFødt": False, "termindato": "2025-09-01"},
    "ARBEIDSSITUASJON": {"status": "jobber", "jobberAnnenPart": True},
    "HVOR_LANG_PERIODE": {"dekningsgrad": "100"},
    "FORDELING": {"antallDagerSøker1": 0},
}

lz = lzstring.LZString()
compressed = lz.compressToEncodedURIComponent(json.dumps(data, ensure_ascii=False))
url = f"https://www.nav.no/foreldrepenger/planlegger/planen-deres?data={compressed}"
```

> Python: `pip install lzstring` er en tredjeparts-reimplementasjon. Verifiser at output er
> kompatibel med JS-`lz-string` (se selvsjekken under) før du stoler på den.

### Steg 4: Selvsjekk før du gir lenken til brukeren

Dekomprimer din egen `data`-verdi og kontroller at den `JSON.parse`-er til nøyaktig objektet du
bygde (lz-string `decompressFromEncodedURIComponent` → `JSON.parse`). Stemmer ikke round-trip,
er lenken ugyldig — ikke send den.

> Kun nøklene i denne guiden er nødvendige. `HVOR_MYE` og `UTTAKSPLAN` finnes i appen, men er
> avledet/valgfrie og skal **utelates** — ikke forsøk å konstruere dem.

---

## Ferdige eksempel-URL-er

### Mor og far, begge jobber, termin 1. sept 2025, 100 %
```
https://www.nav.no/foreldrepenger/planlegger/planen-deres?data=N4IgEgagogsg+gBQDIEEBySoHEtQEogBcoALgJ4AOApkSALYD2ATgPIDmAYgIZMgA0IAHZcAboIQBTmM1oBpHgEt+Q0eIndehECwA2XEAF8BLeACEUeNFAAqRUFSYcAHwBMAzlR1ESTAK5UBLkESLh0dUx5BWgBGZQcIpkEqEmcXEiIAM1CPARIHOgVBFy4SBloAJgAGcoBWAFpKgE4G2KMQC1MoAEkAEQBlPq7rAFUUPoApFjQ7EDcQkl83WgArBgAjNYdlVY2HFEEk8R50wh9-NsgWPDhUNCxEfC6WHqgZlyoAa0FCtjc2Ji4LhilUqhgEHCuLyQXTuMyCITCPS4bAcfScHwcsUIlQMBiAA
```

### Mor alene (aleneomsorg), termin 1. sept 2025, 100 %
```
https://www.nav.no/foreldrepenger/planlegger/planen-deres?data=N4IgEgagogsg+gBQDIEEBySoHEtQEogBcoALgJ4AOApkSALYD2ATiAL4A0IA8vAEIp40UACpFQVJgDEAHwBMAzlQA2REkwCuVTgEMAdiW1KlvbU120AjCE4STZqiRmySRAGaHFnEhLoBLXbLaJAy0AEwADKEArAC04QCccVYcIAK8UACSACIAyjkZwgCqKDkAUlxoYiDyBiTq8rQAVgwARi0S1iDNbRIourpUugimLoTuSoopkFx4cKhoWIj4GVxZUFWyVADWuv4A5vJ7TNqyluHhbKxAA
```

### Far og far, adopsjon, begge jobber, overtakelse 1. juni 2025, 100 %
```
https://www.nav.no/foreldrepenger/planlegger/planen-deres?data=N4IgEgagogsg+gBQDIEEBySoHEtQEogBcoALgJ4AOApkSAGYCGATgPIDmAYsyADQgB2DAG78EAU65NaCKlL6CR4mFQAmjKYRABpAJYBbEAF8+LeACEUeNFAAqRULI4APlQGcqAGyKMP7vg34SBg8PM2Z+WgBGXhAAeyFZIIBrT3dXFQYSWNoAJgAGHIBWAFo8gDZS6L46F3dfDKzcgoAWSuLIwqM+SzMoAEkAEQBlIb6bAFUUIYApFjR7EFcgkgBXV1oAK1iAI23ZGK3d2RR+fipRZhIiEiYVqmNwCBY8OFQ0LER8PpYBqAWVKhJfg6fhsVxsJgMFRRPJ5LogDjPX5IPrvBYBIIhAYMNiyIZOFJMaKEPKGQxAA
```

---

## Viktige regler og begrensninger

| Situasjon | Regel |
|-----------|-------|
| Far og far ved **fødsel** | Støttes ikke av planleggeren. Bruk adopsjon-scenariet for far og far. |
| Ingen jobber | Planleggeren vil ikke vise en plan (ingen rett på ytelse). Informer brukeren. |
| Barn over 3 år | Planleggeren viser ikke plan for barn eldre enn 3 år. |
| `FORDELING` | Utelat dette feltet dersom det er bare én søker, eller dersom far og far ved fødsel. |
| Fellesperiode-dager | Kan ikke overstige total fellesperiode for valgt dekningsgrad. Sett til 0 ved usikkerhet. |

---

## Oppsummering: Nødvendig informasjon å innhente

```
1. Hvem planlegger?          → type i HVEM_PLANLEGGER
2. Navn på partene?          → navnPåMor/navnPåFar/navnPåMedmor/navnPåMedfar (valgfritt)
3. Fødsel, termin eller adopsjon?  → erFødsel + erBarnetFødt i OM_BARNET
4. Relevant dato?            → fødselsdato / termindato / overtakelsesdato
5. Antall barn?              → antallBarn i OM_BARNET
6. Jobber søker 1?           → status i ARBEIDSSITUASJON
7. Jobber søker 2?           → jobberAnnenPart i ARBEIDSSITUASJON
8. 80 % eller 100 %?         → dekningsgrad i HVOR_LANG_PERIODE
9. Dager fellesperiode til søker 1? → antallDagerSøker1 i FORDELING (sett 0 ved usikkerhet)
```
