# grep

`grep` söker efter text i filer eller i utdata från andra kommandon och visar de rader som matchar.

!!! info "Läskommando"
    `grep` visar bara information och ändrar ingenting på systemet.

## Vad är det?

`grep` (*global regular expression print*) är ett av de mest användbara verktygen i Linux. Det letar efter ett **pattern (mönster)** och skriver ut varje rad som innehåller det. Du använder det för att:

- hitta en inställning i en konfigurationsfil
- filtrera fram fel i loggar
- söka igenom många filer på en gång
- korta ner långa utdata från andra kommandon

## Hur fungerar det?

`grep` läser text rad för rad – från filer, eller via en **pipe (rör)** `|` från ett annat kommando – och skriver ut raderna som matchar.

```text
journalctl -u ssh  |  grep "Failed"
      │            │       │
      │            │       └── behåll bara rader som innehåller "Failed"
      │            └────────── skicka utdata vidare som indata
      └─────────────────────── producera många rader
```

Mönstret kan vara vanlig text eller ett **regular expression (reguljärt uttryck)**. Några vanliga tecken:

| Tecken | Betydelse | Exempel |
|--------|-----------|---------|
| `^` | Början av raden | `^#` – rader som börjar med `#` |
| `$` | Slutet av raden | `^$` – tomma rader |
| `.` | Vilket tecken som helst | `h.st` – `hest`, `host`, ... |
| `*` | Föregående tecken noll eller fler gånger | `ab*c` – `ac`, `abc`, `abbc` |
| `[ ]` | Ett av tecknen | `[0-9]` – en siffra |

## Hur använder jag det?

### Grundläggande syntax

```bash
grep [OPTION]... PATTERNS [FILE]...
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-i` | Skiftlägesokänsligt – `error` matchar `Error` och `ERROR` |
| `-r` | Sök rekursivt i en katalog |
| `-n` | Visa radnummer |
| `-v` | Visa rader som *inte* matchar (invert) |
| `-c` | Visa bara antalet matchande rader |
| `-l` | Visa bara namnen på filer som innehåller en träff |
| `-w` | Matcha bara hela ord |
| `-E` | **Extended regular expressions (utökade reguljära uttryck)** – se nedan |
| `-F` | Tolka mönstret som vanlig text, inte som reguljärt uttryck |
| `-A N` / `-B N` / `-C N` | Visa N rader efter / före / runt varje träff |

### Exempel

Hitta en inställning i en konfigurationsfil, med radnummer:

```bash
grep -n "PasswordAuthentication" /etc/ssh/sshd_config
```

Sök efter flera alternativ samtidigt med `-E` och `|` (*eller*):

```bash
grep -E "error|warning" app.log
```

Visa bara aktiva rader – utan kommentarer och tomma rader:

```bash
grep -Ev '^\s*(#|$)' /etc/ssh/sshd_config
```

Sök i alla filer i en katalog:

```bash
grep -rn "192.168.1.10" /etc/
```

Hitta misslyckade inloggningsförsök i loggen:

```bash
sudo journalctl -u ssh | grep -i "failed"
```

Se sammanhanget runt ett fel:

```bash
grep -n -C 3 "error" app.log
```

Räkna förekomster:

```bash
grep -c "404" access.log
```

Använd `grep` i ett script – `-q` ger ingen utdata, bara ett svar via **exit status (avslutningskod)**:

```bash
if grep -q "^anna:" /etc/passwd; then
    echo "Användaren finns"
fi
```

## Att tänka på

- **Citera mönstret** med `'` eller `"`, särskilt när det innehåller mellanslag eller tecken som `*`, `$` och `|`. Enkla citattecken (`'`) är säkrast.
- **Punkt betyder "vilket tecken som helst".** `grep "192.168.1.1"` matchar även `192.168.1.10` och `192x168...`. Använd `-F` för exakt text och `-w` för hela ord.
- **`-r` kan ta lång tid** och ge många `Permission denied` i systemkataloger. Begränsa sökningen till rätt katalog.
- **Exit status:** `0` = träff, `1` = ingen träff, `2` = fel. Användbart i script.
- **Söka efter filnamn** görs med [find](../filsystem/find.md). `grep` söker i filernas *innehåll*.

## Se även

- [find](../filsystem/find.md) – söka efter filer
- [journalctl](../system/journalctl.md) – loggar att filtrera
- [ps](../system/ps.md) – filtrera processlistan
