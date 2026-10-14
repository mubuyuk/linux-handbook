# Linux Handbook

Ett personligt, sökbart uppslagsverk för Linux – skrivet på svenska med
engelska tekniska termer.

Handboken samlar begrepp, kommandon, praktiska exempel och felsökningsmetoder
inom Linux, systemadministration, nätverk, Docker och felsökning. Innehållet
växer successivt utifrån verkliga behov, och gör inte anspråk på att vara en
komplett Linux-kurs.

## Teknik

| Komponent | Val | Varför |
|-----------|-----|--------|
| Innehåll | Markdown | Enkel textfil, lätt att skriva och versionshantera |
| Webbplats | [MkDocs](https://www.mkdocs.org/) + [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) 9.7.7 | Bra sökfunktion och läsbarhet utan egen kod |
| Versionshantering | Git | Historik och möjlighet att ångra ändringar |

Konfigurationen är medvetet minimal: inga plugins utöver den inbyggda
sökfunktionen.

## Katalogstruktur

```text
linux-handbook/
├── README.md        # Den här filen
├── mkdocs.yml       # Webbplatsens konfiguration och navigering
├── mallar/
│   └── artikel.md   # Mall för nya artiklar (publiceras inte)
└── docs/            # Allt innehåll som publiceras
    └── index.md     # Startsida
```

Nya kategorier skapas som underkataloger i `docs/` när den första artikeln i
kategorin skrivs, t.ex. `docs/kommandon/` eller `docs/natverk/`.
Katalog- och filnamn skrivs utan å, ä och ö för att få enkla webbadresser.

## Lägga till en ny artikel

1. Kopiera mallen till rätt kategori:

    ```bash
    mkdir -p docs/kommandon
    cp mallar/artikel.md docs/kommandon/grep.md
    ```

2. Skriv artikeln. Varje artikel besvarar i möjligaste mån:
    - Vad är det?
    - Hur fungerar det?
    - Hur använder jag det?
    - Att tänka på

3. Lägg till artikeln i `nav:` i `mkdocs.yml`:

    ```yaml
    nav:
      - Start: index.md
      - Kommandon:
          - grep: kommandon/grep.md
    ```

4. Förhandsvisa, kontrollera och committa:

    ```bash
    git add docs/kommandon/grep.md mkdocs.yml
    git commit -m "Lägg till artikel om grep"
    ```

Använd generiska exempelvärden i artiklarna (t.ex. `192.168.1.10`,
`server.example`) – aldrig riktiga adresser, lösenord eller nycklar.

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
