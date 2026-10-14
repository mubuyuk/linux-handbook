<!--
Artikelmall för Linux Handbook

Så här används mallen:
1. Kopiera filen till rätt kategori under docs/, t.ex. docs/terminal/cat.md
2. Ersätt alla platshållare inom [hakparenteser].
3. Ta bort avsnitt som inte behövs – en kort artikel är bättre än en utfylld.
4. Lägg till artikeln under nav: i mkdocs.yml.
5. Ta bort den här kommentaren.

Terminologi (se CLAUDE.md):
- Skriv på svenska, men använd etablerade engelska tekniska nyckelord.
- Första förekomsten: **English term (svensk översättning eller förklaring)**,
  t.ex. **File permissions (filbehörigheter)** eller **Owner (ägare)**.
- Därefter kan det engelska begreppet användas utan upprepad översättning.
- Kommandon, flaggor, konfigurationsnycklar och identifierare skrivs exakt
  som i Linux och översätts aldrig.
- Hitta inte på engelska termer – utgå från man-sidor och officiell dokumentation.

Säkerhetsmarkering (se CLAUDE.md):
- Exakt en markering direkt under sammanfattningen, före "Vad är det?".
- info    = kommandot läser eller visar bara information.
- warning = kommandot kan ändra filer, konfiguration eller systemtillstånd.
- danger  = särskilt hög risk för skada eller dataförlust.
- Risknivån beror på hur kommandot används, inte bara på kommandonamnet.
  Kan kommandot både läsa och ändra: använd warning och förklara skillnaden.
- Särskilt farliga användningssätt får även en egen danger-ruta under "Att tänka på".

Tips:
- Förklara kort och konkret. Visa hellre ett bra exempel än tre generiska.
- Använd generiska exempelvärden (192.168.1.10, server.example, användaren "anna"),
  aldrig riktiga IP-adresser, värdnamn, lösenord eller nycklar.
-->

# [Ämne eller kommando]

[En mening som sammanfattar vad artikeln handlar om och när det är användbart.]

!!! info "[Läskommando / Ändrar systemet / Läser eller ändrar]"
    [Byt `info` mot `warning` eller `danger` efter risknivå. Förklara kort vad
    kommandot läser och vad det kan ändra.]

## Vad är det?

[Kort definition. Vad är begreppet eller kommandot, och vilket problem löser det?
Introducera viktiga begrepp som **English term (svensk förklaring)**.]

## Hur fungerar det?

[Förklara principen bakom. Hur hänger det ihop med resten av systemet?
En enkel bild i text eller ett flöde kan hjälpa:]

```text
[indata] ──► [kommando/komponent] ──► [resultat]
```

## Hur använder jag det?

### Grundläggande syntax

```bash
[kommando] [options] [argument]
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `[-x]`          | [Vad flaggan gör] |
| `[-y]`          | [Vad flaggan gör] |

### Exempel

[Beskriv vad exemplet gör innan kodblocket.]

```bash
[exempelkommando]
```

[Visa gärna ett typiskt resultat och förklara det:]

```text
[exempel på utdata]
```

## Att tänka på

- [Vanligt misstag och hur man undviker det]
- [Säkerhetsaspekt eller risk]
- [Skillnad mot ett liknande kommando eller begrepp]

!!! warning "Varning"
    [Använd en varningsruta för kommandon som kan förstöra data eller ändra systemet.]

## Se även

- [Länk till relaterad artikel i handboken](../kategori/artikel.md)
- [`man [kommando]`] – den inbyggda manualsidan
