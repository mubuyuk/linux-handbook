# chmod

`chmod` (*change mode*) ändrar **file permissions (filbehörigheter)** på filer och kataloger – alltså vem som får läsa, ändra eller köra dem.

!!! warning "Ändrar systemet"
    `chmod` ändrar permissions direkt. För breda permissions kan bli en säkerhetsrisk, och för snäva kan få program att sluta fungera.

## Vad är det?

Varje **file (fil)** och **directory (katalog)** i Linux har en uppsättning permissions som styr vad olika användare får göra med den. `chmod` är kommandot för att ändra dessa permissions.

Det används till exempel för att:

- göra ett **script (skript)** körbart
- skydda privata filer, som nycklar och konfiguration, från andra användare
- rätta till permissions när något ger felet `Permission denied`

## Hur fungerar det?

### Tre typer av permissions

| Permission | Bokstav | För en file | För en directory |
|------------|---------|-------------|------------------|
| **Read (läsa)** | `r` | Läsa innehållet | Lista filerna i katalogen |
| **Write (skriva)** | `w` | Ändra innehållet | Skapa, ta bort och byta namn på filer i katalogen |
| **Execute (köra)** | `x` | Köra filen som program eller script | Gå in i katalogen (`cd`) och komma åt filer i den |

!!! note "En directory behöver `x`"
    Utan execute (`x`) på en directory kommer du inte åt något i den, även om
    du har read (`r`). Därför har kataloger nästan alltid `x` där de har `r`.

### Tre klasser av användare

Permissions anges separat för tre klasser av användare:

| Klass | Bokstav i `chmod` | Vem |
|-------|-------------------|-----|
| **Owner (ägare)** | `u` (user) | Användaren som äger filen |
| **Group (grupp)** | `g` (group) | Medlemmar i filens group |
| **Others (övriga)** | `o` (others) | Alla andra användare på systemet |

Bokstaven för owner är `u` eftersom `chmod` kallar ägaren för *user*.

### Läsa permissions med `ls -l`

```bash
ls -l
```

```text
-rw-r--r-- 1 anna anna  1024 okt 14 10:00 anteckningar.txt
-rwxr-xr-x 1 anna anna   512 okt 14 10:05 backup.sh
drwxr-xr-x 2 anna anna  4096 okt 14 10:10 projekt
```

Den första kolumnen läses så här:

```text
 -    rw-     r--     r--
 │     │       │       │
 │     │       │       └── others: read
 │     │       └────────── group:  read
 │     └────────────────── owner:  read + write
 └──────────────────────── file type (filtyp): - = file, d = directory, l = symbolic link
```

Kolumnerna efter siffran visar owner (`anna`) och group (`anna`).

### Numeric mode (numeriskt läge): 4, 2 och 1

I **numeric mode (numeriskt läge)**, även kallat **octal notation (oktal notation)**, har varje permission ett värde. Summan av värdena blir en siffra mellan 0 och 7:

| Permission | Värde |
|------------|-------|
| `r` (read) | 4 |
| `w` (write) | 2 |
| `x` (execute) | 1 |

| Siffra | Uträkning | Permissions |
|--------|-----------|-------------|
| 7 | 4 + 2 + 1 | `rwx` |
| 6 | 4 + 2 | `rw-` |
| 5 | 4 + 1 | `r-x` |
| 4 | 4 | `r--` |
| 0 | – | `---` |

Tre siffror anger permissions för owner, group och others – i den ordningen:

```text
chmod 754 fil
      │││
      ││└── others: 4 = r--
      │└─── group:  5 = r-x
      └──── owner:  7 = rwx
```

## Hur använder jag det?

### Grundläggande syntax

```bash
chmod [OPTION]... MODE FILE...
```

Syntaxen är skriven som i `man chmod`. `MODE` är de permissions som ska sättas, antingen som siffror (`644`) eller symboliskt (`u+x`). `FILE...` är en eller flera filer eller kataloger.

### Vanliga numeriska permissions

| Kommando | Resultat | Används för |
|----------|----------|-------------|
| `chmod 600 fil` | `rw-------` | Privata filer: bara owner kan läsa och skriva |
| `chmod 644 fil` | `rw-r--r--` | Vanliga filer: owner ändrar, alla kan läsa |
| `chmod 755 fil` | `rwxr-xr-x` | Scripts, program och kataloger: owner ändrar, alla kan köra/öppna |

**Exempel: skydda en privat fil**

```bash
chmod 600 hemlig.conf
ls -l hemlig.conf
```

```text
-rw------- 1 anna anna 220 okt 14 10:20 hemlig.conf
```

**Exempel: vanlig textfil som andra får läsa**

```bash
chmod 644 anteckningar.txt
```

**Exempel: göra ett script körbart**

```bash
chmod 755 backup.sh
./backup.sh
```

### Symbolic mode (symboliskt läge)

I **symbolic mode (symboliskt läge)** använder du bokstäver i stället för siffror. Du kan lägga till (`+`), ta bort (`-`) eller sätta (`=`) enskilda permissions. Det är praktiskt när du bara vill ändra en sak och behålla resten:

| Kommando | Betydelse |
|----------|-----------|
| `chmod u+x backup.sh` | Ge owner execute |
| `chmod go-w fil` | Ta bort write för group och others |
| `chmod o= fil` | Ta bort alla permissions för others |
| `chmod a+r fil` | Ge alla (`a`, all) read |

### Recursive (rekursivt) med `-R`

Med `-R` blir ändringen **recursive (rekursiv)**: den gäller en directory och allt innehåll i den.

```bash
chmod -R go-w projekt/
```

## Att tänka på

!!! danger "Undvik `chmod 777`"
    `777` (`rwxrwxrwx`) betyder att **alla** användare får read, write och execute
    på filen. Det "löser" ofta ett `Permission denied`, men öppnar en säkerhetsrisk:
    vem som helst kan ändra innehållet. Är det ett script som körs av
    **root (superanvändaren)** kan en annan användare i praktiken köra egna
    kommandon som root.

- **Fel owner är ofta det egentliga problemet.** Om du inte kommer åt en fil beror det ofta på att den ägs av fel användare. Då är `chown` rätt verktyg, inte bredare permissions.
- **Var försiktig med `-R` och siffror.** `chmod -R 755 katalog/` gör *alla filer* körbara, inte bara katalogerna. Använd hellre symbolic mode, t.ex. `chmod -R go-w`. Stora `X` (`chmod -R a+rX katalog/`) sätter execute bara på kataloger och på filer som redan är körbara.
- **Bara owner (eller root) kan köra `chmod`** på en fil. Annars blir det `Operation not permitted`.
- **Att ta bort en fil styrs av katalogen.** Rätten att radera en fil beror på write för den *directory* den ligger i, inte på filens egna permissions.
- **En private key (privat nyckel) måste vara skyddad.** SSH vägrar till exempel använda en private key som andra kan läsa – den ska ha `600`.
- **Kontrollera alltid resultatet** med `ls -l` efter en ändring.

## Se även

- `man chmod` – den inbyggda manualsidan
- [chown](chown.md) – ändra owner och group för filer
- [ls](../terminal/ls.md) – visa permissions med `ls -l`
