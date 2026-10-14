<!--
Artikelmall för Linux Handbook

Så här används mallen:
1. Kopiera filen till rätt kategori under docs/, t.ex. docs/kommandon/grep.md
2. Ersätt alla platshållare inom [hakparenteser].
3. Ta bort avsnitt som inte behövs – en kort artikel är bättre än en utfylld.
4. Lägg till artikeln under nav: i mkdocs.yml.
5. Ta bort den här kommentaren.

Tips:
- Skriv på svenska, men behåll engelska tekniska termer (t.ex. "process", "pipe").
- Förklara kort och konkret. Visa hellre ett bra exempel än tre generiska.
- Använd generiska exempelvärden (192.168.1.10, server.example, användaren "anna"),
  aldrig riktiga IP-adresser, värdnamn, lösenord eller nycklar.
-->

# [Ämne eller kommando]

[En mening som sammanfattar vad artikeln handlar om och när det är användbart.]

## Vad är det?

[Kort definition. Vad är begreppet eller kommandot, och vilket problem löser det?]

## Hur fungerar det?

[Förklara principen bakom. Hur hänger det ihop med resten av systemet?
En enkel bild i text eller ett flöde kan hjälpa:]

```text
[indata] ──► [kommando/komponent] ──► [resultat]
```

## Hur använder jag det?

### Grundläggande syntax

```bash
[kommando] [flaggor] [argument]
```

| Flagga / del | Betydelse |
|--------------|-----------|
| `[-x]`       | [Vad flaggan gör] |
| `[-y]`       | [Vad flaggan gör] |

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
