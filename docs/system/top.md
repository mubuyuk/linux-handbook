# top

`top` visar i realtid vilka processer som körs och hur mycket CPU och minne de använder.

!!! info "Läskommando – med ett undantag"
    `top` visar bara information. Undantaget är tangenten `k`, som skickar en signal och kan avsluta en process.

## Vad är det?

`top` är ett **interactive (interaktivt)** övervakningsverktyg. Det uppdateras var tredje sekund och svarar på frågor som:

- Varför är servern långsam just nu?
- Vilken **process** använder mest CPU eller minne?
- Hur hög är **load average (systembelastning)**?

## Hur fungerar det?

Skärmen har två delar: en sammanfattning högst upp och en processlista under.

### Sammanfattningen

```text
top - 10:00:00 up 6 days,  2:15,  1 user,  load average: 0.42, 0.35, 0.30
Tasks: 210 total,   1 running, 209 sleeping,   0 stopped,   0 zombie
%Cpu(s):  3.1 us,  1.2 sy,  0.0 ni, 95.5 id,  0.2 wa,  0.0 hi,  0.0 si,  0.0 st
MiB Mem :   8062.0 total,   1200.0 free,   2500.0 used,   4362.0 buff/cache
MiB Swap:    512.0 total,    512.0 free,      0.0 used.   5562.0 avail Mem
```

| Del | Betydelse |
|-----|-----------|
| `up 6 days` | **Uptime (drifttid)** sedan senaste omstart |
| `load average` | Genomsnittligt antal processer som kör eller väntar, för 1, 5 och 15 minuter |
| `us` / `sy` | CPU-tid för vanliga program / för kärnan (**kernel**) |
| `id` | Ledig CPU-tid (idle) |
| `wa` | CPU väntar på disk eller annan I/O (**I/O wait**) |
| `avail Mem` | Minne som kan användas av nya program |

En tumregel för `load average`: jämför med antalet CPU-kärnor. På en dator med 4 kärnor betyder ett värde under 4 att CPU:n hinner med.

### Processlistan

| Kolumn | Betydelse |
|--------|-----------|
| `PID` | **Process ID (process-ID)** – processens unika nummer |
| `USER` | Användaren som kör processen |
| `RES` | Fysiskt minne (RAM) som processen använder |
| `S` | Status: `R` kör, `S` sover, `D` väntar på I/O, `Z` zombie |
| `%CPU` / `%MEM` | Andel CPU / minne |
| `COMMAND` | Programmets namn |

## Hur använder jag det?

### Grundläggande syntax

```bash
top [OPTION]...
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-o FÄLT` | Sortera efter ett fält, t.ex. `-o %MEM` |
| `-u ANVÄNDARE` | Visa bara en användares processer |
| `-b -n 1` | Skriv ut en gång och avsluta (batch mode) – bra i script |

### Tangenter medan `top` körs

| Tangent | Funktion |
|---------|----------|
| `P` | Sortera efter CPU |
| `M` | Sortera efter minne |
| `1` | Visa varje CPU-kärna för sig |
| `c` | Visa hela kommandoraden |
| `h` | Hjälp |
| `k` | Avsluta (kill) en process – frågar efter PID |
| `q` | Avsluta `top` |

### Exempel

Starta `top`:

```bash
top
```

Sortera direkt efter minnesanvändning:

```bash
top -o %MEM
```

Spara en ögonblicksbild till en fil, t.ex. för felsökning:

```bash
top -b -n 1 > top-snapshot.txt
```

## Att tänka på

- **Hög `buff/cache` är normalt.** Linux använder ledigt minne som cache och lämnar tillbaka det när program behöver det. Titta på `avail Mem`, inte `free`. Se [free](free.md).
- **Högt `wa`** tyder på att disken är flaskhalsen, inte CPU:n.
- **Var försiktig med `k`.** Att avsluta fel process kan stoppa en tjänst. Se [kill](kill.md).
- **`htop`** är ett populärt alternativ med färger och musstöd. Det installeras separat med `sudo apt install htop`.

## Se även

- [ps](ps.md) – ögonblicksbild av processer
- [kill](kill.md) – avsluta processer
- [free](free.md) – minnesanvändning
