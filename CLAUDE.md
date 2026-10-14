# Linux Handbook – riktlinjer för dokumentation

Det här repot innehåller en personlig, sökbar Linux-handbok byggd med
MkDocs Material (9.7.7) och Markdown. Innehållet växer successivt utifrån
verkliga behov – skriv inte artiklar "för säkerhets skull".

## Språk och teknisk terminologi

- Skriv artiklarna på **svenska**.
- Använd alltid **etablerade engelska tekniska nyckelord**.
- Ge en svensk översättning eller förklaring i anslutning till begreppet.
- Vid **första förekomsten** i en artikel: `**English term (svensk översättning eller förklaring)**`.
- **Därefter** kan det engelska begreppet användas utan att översättningen upprepas.
- Behåll **kommandon, flaggor, konfigurationsnycklar och tekniska identifierare
  exakt** som de skrivs i Linux (t.ex. `chmod`, `-R`, `u+x`, `PermitRootLogin`).
  Översätt dem aldrig.
- Använd inte påhittade engelska termer när det redan finns etablerad teknisk
  terminologi. Utgå från hur begreppet heter i man-sidor och officiell dokumentation.

Exempel:

- **File permissions (filbehörigheter)**
- **Owner (ägare)**
- **Group (grupp)**
- **Read (läsa)**
- **Write (skriva)**
- **Execute (köra)**

## Artikelstruktur

Utgå från `mallar/artikel.md`. Varje artikel besvarar i möjligaste mån:

1. Vad är det?
2. Hur fungerar det?
3. Hur använder jag det?
4. Att tänka på

Avsluta gärna med "Se även".

## Säkerhetsmarkering

Varje artikel har **en** markering (admonition) direkt under den inledande
sammanfattningen, före "Vad är det?". Den visar läsaren hur riskfyllt kommandot är.

| Nivå | Används för | Exempel |
|------|-------------|---------|
| `!!! info` | Kommandon som **endast läser** eller visar information | `ls`, `ps`, `df`, `dig` |
| `!!! warning` | Kommandon som **kan ändra** filer, konfiguration eller systemtillstånd | `cp`, `mv`, `systemctl`, `apt` |
| `!!! danger` | Kommandon eller användningssätt med **särskilt hög risk** för skada eller dataförlust | `rm`, `sudo`, `kill`, `chown` |

Format:

```markdown
!!! warning "Läser eller ändrar"
    `systemctl status` läser bara. `start`, `stop` och `restart` ändrar systemet.
```

Riktlinjer:

- **Risknivån beror på hur kommandot används, inte bara på kommandonamnet.**
  `find` är ofarligt för att söka, men `find ... -delete` kan radera många filer.
  `curl` läser normalt, men `curl ... | sudo bash` kör okänd kod som root.
- Välj nivå efter hur kommandot används i artikeln. Kan det både läsa och ändra:
  använd `warning` och förklara i rutan vilka användningssätt som läser och vilka som ändrar.
- Har ett i övrigt läsande kommando ett ändrande undantag, nämn undantaget i
  markeringen (t.ex. "Läskommando – med ett undantag").
- Särskilt farliga användningssätt får dessutom en egen `!!! danger`-ruta där de
  beskrivs, oftast under "Att tänka på".
- Rutans text följer samma terminologiregler som resten av artikeln.

## Innehåll och stil

- Nybörjarvänligt, kort och konkret. Hellre ett bra exempel än tre generiska.
- Använd tydliga rubriker, tabeller och kodblock (`bash` för kommandon, `text` för utdata).
- Undvik långa, generiska texter som inte tillför något.
- Använd admonitions (`!!! warning`, `!!! danger`, `!!! note`) för risker och viktiga undantag,
  utöver säkerhetsmarkeringen överst (se ovan).
- Använd **generiska exempelvärden**: `192.168.1.10`, `server.example`, användaren `anna`.
  Skriv aldrig in riktiga IP-adresser, värdnamn, lösenord, nycklar eller andra
  uppgifter om en specifik server – repot är publikt.
- Håll generell Linux-kunskap åtskild från serverspecifik konfiguration.

## Struktur och navigering

- Artiklar ligger i kategorikataloger under `docs/`, t.ex. `docs/filsystem/chmod.md`.
- Katalog- och filnamn skrivs utan å, ä och ö.
- Skapa en kategori först när dess första artikel skrivs – inga tomma kategorier
  eller platshållarartiklar.
- Varje ny artikel läggs till explicit under `nav:` i `mkdocs.yml`.
- Länka bara till sidor som finns. Trasiga länkar får `--strict` att misslyckas.

## Verifiering

Bygg alltid med strict-läge efter en ändring, i en tillfällig container:

```bash
docker run --rm --network none -v "$PWD":/docs:ro --entrypoint mkdocs \
  squidfunk/mkdocs-material:9.7.7 build --strict --site-dir /tmp/site
```

## Arbetsflöde

- Gör små ändringar och verifiera dem innan commit.
- Gör inte commit eller push utan uttryckligt godkännande.
