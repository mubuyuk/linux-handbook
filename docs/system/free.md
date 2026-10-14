# free

`free` visar hur mycket minne (RAM) och swap som används och finns tillgängligt.

!!! info "Läskommando"
    `free` visar bara information och ändrar ingenting på systemet.

## Vad är det?

`free` ger en snabb översikt över **memory (arbetsminne, RAM)**. Det används för att avgöra om servern har tillräckligt med minne, eller om minnesbrist kan vara orsaken till att den är långsam eller att program avslutas oväntat.

## Hur fungerar det?

```text
               total        used        free      shared  buff/cache   available
Mem:           7.9Gi       2.4Gi       1.2Gi        50Mi       4.3Gi       5.4Gi
Swap:          511Mi          0B       511Mi
```

| Kolumn | Betydelse |
|--------|-----------|
| `total` | Totalt minne |
| `used` | Använt av program |
| `free` | Helt oanvänt |
| `shared` | Delat minne, bland annat `tmpfs` |
| `buff/cache` | Används som **cache (cacheminne)** för filer och disk |
| `available` | Uppskattning av hur mycket nya program kan använda |

**Den viktigaste kolumnen är `available`, inte `free`.** Linux använder ledigt minne som cache för att göra filåtkomst snabbare. Den cachen lämnas tillbaka direkt när ett program behöver minnet. Ett lågt `free` är därför normalt och bra.

**Swap (växlingsutrymme)** är diskutrymme som används när RAM tar slut. Det förhindrar krascher, men är mycket långsammare än RAM.

## Hur använder jag det?

### Grundläggande syntax

```bash
free [OPTION]...
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `-h` | Läsbara enheter (Mi, Gi) – human-readable |
| `-m` / `-g` | Visa i MiB / GiB |
| `-s N` | Uppdatera var N:e sekund – avsluta med `Ctrl+C` |
| `-t` | Lägg till en rad med summan av RAM och swap |

### Exempel

Snabb översikt:

```bash
free -h
```

Följ minnet medan du startar något tungt, t.ex. en container:

```bash
free -h -s 2
```

Se vilka processer som använder mest minne:

```bash
ps aux --sort=-%mem | head -n 6
```

## Att tänka på

- **Titta på `available`.** Om den är låg *och* swap används mycket har servern för lite minne.
- **Lite använd swap är inget problem.** Kärnan flyttar ibland sällan använda data dit. Det är när swap används *aktivt* hela tiden som det blir långsamt.
- **När minnet tar slut helt** avslutar kärnans **OOM killer (out-of-memory killer)** en process för att rädda systemet. Det syns i `journalctl -k` som `Out of memory`.
- **Per container:** `docker stats` visar minnesanvändningen för varje container.

## Se även

- [top](top.md) – minne och CPU i realtid
- [ps](ps.md) – vilka processer som använder minne
- [df](df.md) – diskutrymme
