# Linux Handbook

Ett personligt, sökbart uppslagsverk för Linux – skrivet på svenska med
etablerade engelska tekniska termer.

Handboken samlar kommandon, begrepp, praktiska exempel och felsökningsmetoder
som jag använder eller har lärt mig. Den utgår främst från Debian och vanliga
scenarier på en hemmaserver. Innehållet växer successivt utifrån verkliga behov,
och gör inte anspråk på att vara en komplett Linux-kurs.

## Teknik

| Komponent | Val | Varför |
|-----------|-----|--------|
| Innehåll | Markdown | Enkla textfiler, lätta att skriva och versionshantera |
| Webbplats | [MkDocs](https://www.mkdocs.org/) + [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) 9.7.7 | Bra sökfunktion och läsbarhet utan egen kod |
| Versionshantering | Git | Historik och möjlighet att ångra ändringar |

Konfigurationen är medvetet minimal: inga plugins utöver den inbyggda
sökfunktionen (svenska och engelska).

## Innehåll

30 artiklar i fem kategorier:

| Kategori | Katalog | Artiklar |
|----------|---------|----------|
| Terminal & kommandon | `docs/terminal/` | `pwd`, `ls`, `cd`, `mkdir`, `cp`, `mv`, `rm` |
| Filsystem & rättigheter | `docs/filsystem/` | `chmod`, `chown`, `sudo`, `find`, `ln` |
| System & processer | `docs/system/` | `top`, `ps`, `kill`, `systemctl`, `journalctl`, `df`, `free` |
| Nätverk & felsökning | `docs/natverk/` | `ip`, `ping`, `curl`, `ss`, `dig`, `traceroute` |
| Paket & fjärråtkomst | `docs/paket-fjarratkomst/` | `apt`, `ssh`, `scp`, `tar`, `grep` |

Varje artikel besvarar fyra frågor – *Vad är det? Hur fungerar det? Hur använder
jag det? Att tänka på* – och har en säkerhetsmarkering överst som visar om
kommandot bara läser (info), kan ändra systemet (warning) eller har särskilt
hög risk (danger).

## Dokumentationsregler

Reglerna för språk, terminologi, säkerhetsmarkeringar, struktur och
verifiering finns i [`CLAUDE.md`](CLAUDE.md). Kortfattat:

- Svensk text med etablerade engelska tekniska nyckelord. Första förekomsten
  skrivs **English term (svensk förklaring)**.
- Kommandon, flaggor och identifierare skrivs exakt som i Linux.
- Generiska exempelvärden, aldrig riktiga adresser, lösenord eller nycklar.

## Katalogstruktur

```text
linux-handbook/
├── README.md                  # Den här filen
├── CLAUDE.md                  # Dokumentationsregler
├── mkdocs.yml                 # Webbplatsens konfiguration och navigering
├── mallar/
│   └── artikel.md             # Mall för nya artiklar (publiceras inte)
└── docs/                      # Allt innehåll som publiceras
    ├── index.md               # Startsida
    ├── terminal/              # Terminal & kommandon
    ├── filsystem/             # Filsystem & rättigheter
    ├── system/                # System & processer
    ├── natverk/               # Nätverk & felsökning
    └── paket-fjarratkomst/    # Paket & fjärråtkomst
```

Katalog- och filnamn skrivs utan å, ä och ö för att få enkla webbadresser.
En ny kategori skapas först när dess första artikel skrivs.

## Lägga till en ny artikel

1. Kopiera mallen till rätt kategori:

    ```bash
    cp mallar/artikel.md docs/terminal/cat.md
    ```

2. Skriv artikeln enligt mallen och reglerna i `CLAUDE.md`, inklusive
   säkerhetsmarkeringen överst.

3. Lägg till artikeln under rätt grupp i `nav:` i `mkdocs.yml`:

    ```yaml
    nav:
      - Terminal & kommandon:
          - cat: terminal/cat.md
    ```

4. Länka gärna till den från startsidan (`docs/index.md`) och från relaterade
   artiklar under "Se även".

5. Kontrollbygg (se nedan) och committa:

    ```bash
    git add docs/terminal/cat.md mkdocs.yml docs/index.md
    git commit -m "Lägg till artikel om cat"
    ```

## Förhandsvisa lokalt med Docker

Kräver endast Docker. Kör kommandona från projektets rotkatalog.

**Live-förhandsvisning** (uppdateras automatiskt när en fil sparas):

```bash
docker run --rm -it -p 8000:8000 -v "$PWD":/docs:ro \
  squidfunk/mkdocs-material:9.7.7
```

Öppna sedan <http://localhost:8000> i webbläsaren. Avsluta med `Ctrl+C`.

**Kontrollbygge** (avbryter vid varningar, t.ex. trasiga länkar):

```bash
docker run --rm --user "$(id -u):$(id -g)" -v "$PWD":/docs \
  squidfunk/mkdocs-material:9.7.7 build --strict
```

Den genererade webbplatsen hamnar i `site/`, som inte versionshanteras.
`--user` gör att filerna ägs av din egen användare i stället för `root`.
