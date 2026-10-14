# find

`find` söker efter filer och kataloger utifrån namn, typ, storlek, ålder, ägare och mycket mer.

!!! warning "Läser – men kan ändra"
    Att söka med `find` är ofarligt. Med `-delete` eller `-exec` kan `find` däremot ta bort eller ändra många filer på en gång.

## Vad är det?

`find` går igenom ett katalogträd och skriver ut alla filer som matchar dina villkor. Det är verktyget när du behöver svara på frågor som:

- Var ligger filen `docker-compose.yml`?
- Vilka filer är större än 1 GB?
- Vilka loggfiler har inte ändrats på 30 dagar?
- Vilka filer kan ändras av alla användare?

## Hur fungerar det?

`find` tar en startkatalog och en serie **expressions (uttryck)** som läses från vänster till höger:

```text
find /var/log  -type f  -name "*.log"  -mtime +30
     │         │        │              │
     │         │        │              └── ändrad för mer än 30 dagar sedan
     │         │        └───────────────── namnet slutar på .log
     │         └────────────────────────── bara vanliga filer
     └──────────────────────────────────── börja leta här
```

Alla villkor måste vara uppfyllda (logiskt *och*). Utan någon **action (åtgärd)** skrivs matchande sökvägar ut.

## Hur använder jag det?

### Grundläggande syntax

```bash
find [STARTKATALOG...] [EXPRESSION]
```

| Test | Betydelse |
|------|-----------|
| `-name "MÖNSTER"` | Filnamnet matchar mönstret (skiftlägeskänsligt) |
| `-iname "MÖNSTER"` | Som `-name`, men skiftlägesokänsligt |
| `-type f` / `-type d` / `-type l` | Fil / katalog / symbolisk länk |
| `-size +100M` | Större än 100 MiB (`k`, `M`, `G`; `-` betyder mindre än) |
| `-mtime +30` | Ändrad för mer än 30 dagar sedan (`-30` = inom 30 dagar) |
| `-mmin -60` | Ändrad inom de senaste 60 minuterna |
| `-user anna` | Ägs av användaren `anna` |
| `-perm -o+w` | Others har write (skrivbar för alla) |
| `-maxdepth N` | Gå högst N nivåer ner (skrivs först bland villkoren) |
| `-xdev` | Stanna på samma filsystem |

| Action (åtgärd) | Betydelse |
|-----------------|-----------|
| `-print` | Skriv ut sökvägen (standard) |
| `-ls` | Skriv ut i ett format som liknar `ls -l` |
| `-exec KOMMANDO {} \;` | Kör ett kommando för varje träff |
| `-exec KOMMANDO {} +` | Kör kommandot en gång med många träffar (snabbare) |
| `-delete` | Ta bort träffarna |

### Exempel

Hitta en fil efter namn i din home directory:

```bash
find ~ -name "docker-compose.yml"
```

Hitta stora filer på hela systemet, utan felmeddelanden om kataloger du inte får läsa:

```bash
sudo find / -xdev -type f -size +1G 2>/dev/null
```

Hitta filer som ändrats den senaste timmen i `/etc`:

```bash
sudo find /etc -type f -mmin -60
```

Hitta filer som alla användare kan skriva till (säkerhetskontroll):

```bash
find /srv -type f -perm -o+w
```

Lista detaljer för träffarna:

```bash
find /var/log -name "*.gz" -ls
```

Kör ett kommando på träffarna, t.ex. sätta permissions på alla filer men inte kataloger:

```bash
find ~/projekt -type f -exec chmod 644 {} +
```

### Ta bort filer säkert

Gör alltid i två steg: lista först, ta bort sedan.

```bash
find /tmp/minapp -name "*.tmp" -mtime +7          # 1. granska listan
find /tmp/minapp -name "*.tmp" -mtime +7 -delete  # 2. samma kommando + -delete
```

## Att tänka på

!!! danger "Placeringen av `-delete` spelar roll"
    `find` läser uttrycket från vänster till höger. `find . -delete -name "*.tmp"`
    tar bort **allt** i katalogen innan namnet ens kontrolleras. `-delete` ska alltid
    stå sist.

- **Citera mönster.** Skriv `-name "*.log"`, inte `-name *.log` – annars kan skalet expandera `*` innan `find` får se det.
- **`2>/dev/null` döljer felmeddelanden** som `Permission denied`. Praktiskt, men det döljer också riktiga fel.
- **`-xdev` hindrar sökningen från att gå in i andra filsystem**, t.ex. nätverksdiskar eller `/proc`. Bra vid sökningar från `/`.
- **`-mtime` räknar i hela dygn.** `-mtime +30` betyder i praktiken "äldre än 31 dagar".

## Se även

- [grep](../paket-fjarratkomst/grep.md) – söka i filers *innehåll*
- [ls](../terminal/ls.md) – lista en enskild katalog
- [rm](../terminal/rm.md) – ta bort filer
