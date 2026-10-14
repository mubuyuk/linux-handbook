# ls

`ls` (*list*) listar filer och kataloger – och med rätt flaggor även storlek, ägare, behörigheter och datum.

!!! info "Läskommando"
    `ls` visar bara information och ändrar ingenting på systemet.

## Vad är det?

`ls` är ett av de mest använda kommandona i Linux. Utan argument listar det innehållet i din **working directory (arbetskatalog)**. Med ett **argument** (en sökväg) listar det en annan katalog eller visar information om en enskild fil.

Du använder det för att:

- se vilka filer som finns i en katalog
- kontrollera **file permissions (filbehörigheter)**, ägare och storlek
- hitta de senast ändrade eller största filerna

## Hur fungerar det?

`ls` läser katalogens innehåll och skriver ut namnen. Filer som börjar med punkt, t.ex. `.bashrc`, är **hidden files (dolda filer)** och visas bara med `-a`.

Med `-l` (long listing format) visas en rad per fil:

```text
-rw-r--r-- 1 anna anna  4.2K okt 14 10:00 anteckningar.txt
│          │ │    │     │    │            │
│          │ │    │     │    │            └── namn
│          │ │    │     │    └─────────────── senast ändrad
│          │ │    │     └──────────────────── storlek
│          │ │    └────────────────────────── group (grupp)
│          │ └─────────────────────────────── owner (ägare)
│          └───────────────────────────────── antal hard links (hårda länkar)
└──────────────────────────────────────────── filtyp och permissions
```

## Hur använder jag det?

### Grundläggande syntax

```bash
ls [OPTION]... [FILE]...
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-l` | Långt format med permissions, ägare, storlek och datum |
| `-a` | Visa även hidden files (inklusive `.` och `..`) |
| `-A` | Som `-a`, men utan `.` och `..` |
| `-h` | Storlekar i läsbar form (K, M, G) – används tillsammans med `-l` |
| `-t` | Sortera efter ändringstid, nyaste först |
| `-S` | Sortera efter storlek, största först |
| `-r` | Omvänd sortering |
| `-d` | Visa själva katalogen, inte dess innehåll |
| `-R` | Lista rekursivt, inklusive alla underkataloger |

Flaggor kan kombineras: `ls -lah` är samma sak som `ls -l -a -h`.

### Exempel

Lista allt i den aktuella katalogen, med detaljer och läsbara storlekar:

```bash
ls -lah
```

Visa de senast ändrade filerna sist (praktiskt i loggkataloger):

```bash
ls -ltr /var/log
```

Hitta de största filerna i en katalog:

```bash
ls -lhS ~/Downloads
```

Kontrollera permissions på själva katalogen, inte innehållet:

```bash
ls -ld /srv
```

```text
drwxr-xr-x 5 root root 4096 sep  7 14:51 /srv
```

Lista bara filer som matchar ett mönster med **wildcard (jokertecken)**:

```bash
ls -l *.conf
```

## Att tänka på

- **`*` expanderas av skalet, inte av `ls`.** `ls *.conf` får en lista med filnamn av skalet. Matchar inget skickas `*.conf` vidare bokstavligt och `ls` svarar `No such file or directory`.
- **Använd inte utdata från `ls` i script.** Filnamn med mellanslag eller specialtecken tolkas lätt fel. Använd [find](../filsystem/find.md) eller skalets wildcards i stället.
- `-h` påverkar bara visningen av storlek. För diskutrymme, använd [df](../system/df.md); för storlek på kataloger, `du -sh katalog/`.
- Färgerna i utdata kommer ofta från ett **alias** i `~/.bashrc` (`ls --color=auto`).

## Se även

- [chmod](../filsystem/chmod.md) – förstå och ändra permissions
- [cd](cd.md) – byta katalog
- [find](../filsystem/find.md) – söka efter filer
