# ps

`ps` (*process status*) visar en ögonblicksbild av processerna som körs.

!!! info "Läskommando"
    `ps` visar bara information och ändrar ingenting på systemet.

## Vad är det?

Varje program som körs är en eller flera **processes (processer)**. Varje process har ett **PID (process-ID)**. `ps` listar processerna och används för att:

- kontrollera om ett program körs
- hitta PID för en process, t.ex. innan du använder [kill](kill.md)
- se vem som startade en process och med vilka argument

Till skillnad från [top](top.md) uppdateras `ps` inte – den visar läget i just det ögonblicket. Därför passar den bra ihop med andra kommandon, t.ex. `grep`.

## Hur fungerar det?

Utan flaggor visar `ps` bara processerna i din egen terminal. För att se *alla* processer används nästan alltid en av dessa två varianter:

| Kommando | Stil | Visar |
|----------|------|-------|
| `ps aux` | BSD-stil (utan bindestreck) | Alla processer, med CPU och minne |
| `ps -ef` | UNIX-stil | Alla processer, med **PPID (parent process ID, förälderns PID)** |

Kolumner i `ps aux`:

| Kolumn | Betydelse |
|--------|-----------|
| `USER` | Användaren som kör processen |
| `PID` | Process-ID |
| `%CPU` / `%MEM` | Andel CPU / minne |
| `RSS` | Fysiskt minne i KiB |
| `STAT` | Status, t.ex. `R` kör, `S` sover, `Z` zombie |
| `START` | När processen startade |
| `COMMAND` | Kommandot med argument |

## Hur använder jag det?

### Grundläggande syntax

```bash
ps [OPTION]...
```

| Option (flagga) | Betydelse |
|-----------------|-----------|
| `aux` | Alla processer, med användare och resursanvändning |
| `-ef` | Alla processer, i fullständigt format |
| `-C NAMN` | Bara processer med ett visst kommandonamn |
| `-u ANVÄNDARE` | Bara en användares processer |
| `-o FÄLT,...` | Välj egna kolumner |
| `--sort=-%mem` | Sortera, `-` betyder fallande |
| `--forest` | Visa processerna som ett träd |

### Exempel

Alla processer:

```bash
ps aux
```

Kontrollera om en viss tjänst körs:

```bash
ps -C sshd -o pid,user,cmd
```

Söka i processlistan med `grep`:

```bash
ps aux | grep nginx
```

De fem processer som använder mest minne:

```bash
ps aux --sort=-%mem | head -n 6
```

(`head -n 6` ger rubrikraden plus fem processer.)

Visa förälder–barn-relationer:

```bash
ps -ef --forest
```

## Att tänka på

- **`ps aux | grep nginx` hittar också sig själv**, eftersom `grep nginx` också är en process. Använd `pgrep -a nginx` för en renare lista.
- **`ps aux` utan bindestreck är avsiktligt.** `ps -aux` fungerar oftast men betyder formellt något annat.
- **Processer i Docker-containrar syns också** i `ps aux` på värddatorn, ofta med en annan användare eller ett UID.
- **`Z` (zombie)** är en avslutad process som föräldern inte städat upp. Enstaka zombies är ofarliga.

## Se även

- [top](top.md) – processer i realtid
- [kill](kill.md) – avsluta processer
- [grep](../paket-fjarratkomst/grep.md) – filtrera utdata
